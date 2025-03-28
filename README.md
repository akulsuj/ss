# -*- coding: utf-8 -*-
"""
@author: John Hancock
"""
import pandas as pd
import sqlalchemy
import logging
from sqlalchemy.orm import Session 
from sqlalchemy import MetaData , func
import urllib
import Entities.dbormschemas as dbsch
from datetime import datetime
#from O365.utils import BaseTokenBackend
import globalvars as gvar

logger = logging.getLogger(__name__)

class TranInprogress(Exception):
    pass

class dboperations():
    
    def __init__(self, newsyssession=None):
        super().__init__()  

        self.params = gvar.sqlconfig
        self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
        self.connection = self.engine.connect()
        self.dbapi_rawconnection = self.connection.connection
        self.session = Session(self.engine, expire_on_commit=False)
        self.metadata = MetaData()
        self.newsession = newsyssession
        self.dbsyssess = None
        self.sysdlrec = None
        self.actionLogRec = None
        self.usersRec = None
        self.rolesRec = None
        self.userDetailsRec = None

    def truncatetable(self,tablename, paramCondition = None):
        logging.debug("In truncatetable() method") 
        try:
            if tablename == 'SADRD_SrcStaging_SchDAllPartsdata' and paramCondition == 'Yes':
                truncate_query = sqlalchemy.text("delete from  " + tablename)
            elif tablename == 'SADRD_FactsTemp_CntrlTotals_Input':
                if paramCondition == 'nonQualFTC':
                    truncate_query = sqlalchemy.text("delete from  " + tablename + " where ReportType in ('Funds', 'I&I', 'All', '')")
                elif paramCondition == 'QualFTC':
                    truncate_query = sqlalchemy.text("delete from  " + tablename + " where PartType in ('QualPctFTC')")
                elif paramCondition == 'FTCGrossup':
                    truncate_query = sqlalchemy.text("delete from  " + tablename + " where PartType in ('FTCGrossup')")
                elif paramCondition == 'GL':
                    truncate_query = sqlalchemy.text("delete from  " + tablename + " where PartType in ('GL')")
            elif tablename == 'SADRD_Factstemp_Exception':
                if paramCondition == 'Sch D Part':
                    truncate_query = sqlalchemy.text("delete from  " + tablename + " where ExceptionType in ('Sch D Part')")
                elif paramCondition == 'QualPctFTC':
                    truncate_query = sqlalchemy.text("delete from  " + tablename + " where ExceptionType in ('QualPctFTC')")
                elif paramCondition == 'FTCGrossup':
                    truncate_query = sqlalchemy.text("delete from  " + tablename + " where ExceptionType in ('FTCGrossup')")
            else:
                truncate_query = sqlalchemy.text("delete from  " + tablename )
            self.connection.execution_options(autocommit=True).execute(truncate_query)
        except Exception as e:
            logging.exception("Exception occurred in truncatetable() method :: " + str(e))
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, tablename, 'truncatetable', str(datetime.now())[0:23], ("Exception occurred in truncatetable - " + str(e)), None)
            logger.error('Error occured while truncating table: ' + tablename + ' Error :' + str(e))
            raise e

    def loaddata(self, tablename, df, copyoption = '', chunksize = 1000):
        logging.debug("In loaddata() method") 
        try:
            if tablename != "SADRD_SrcStaging_CusipQualFTC" and tablename != "SADRD_SrcStaging_BankGrossUpPct" and tablename != 'SADRD_FactsTemp_CntrlTotals_Input':
                self.truncatetable(tablename)
                #To handle commit/rollback, using engine context manager
            with self.engine.begin() as conn:
                if tablename == 'SADRD_SrcStaging_SchDAllPartsdata':
                    df['dataload_key'] = self.sysdlrec.dataload_key

            #df['period'] = period
                if copyoption == 'ormbulkinsert':
                    #Very slow , > 5 mins and dataloads table update failing
                    listToWrite = df.to_dict(orient='records')
                    table = sqlalchemy.Table(tablename, self.metadata, autoload=True)
                    self.connection.execute(table.insert(), listToWrite)
                else:
                    #10k takes 10 seconds
                    tsql_chunksize = 2097 // len(df.columns)
                    chunksize = 1000 if tsql_chunksize > 1000 else tsql_chunksize
                    df.to_sql(name=tablename,con=conn, if_exists='append',schema = 'dbo',index=False,method='multi',chunksize=chunksize)
                    self.update_dataloadkey(gvar.COMPLETED)
        except Exception as e:
            logging.exception("Exception occurred in loaddata() method :: " + str(e))
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, tablename, 'loaddata', str(datetime.now())[0:23], ("Exception occurred in loaddata - " + str(e)), None)
            self.update_dataloadkey(gvar.FAILED,str(e))
            logger.error('Data load failed: '+ str(e))
            raise e

        self.session.commit()
    
    def insert_dataloadkey(self, loadkey, strJsonVal, src, status = '', cycleDate = '', importedby = '', comments = ''):
        logging.debug("In insert_dataloadkey() method") 
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
            self.connection = self.engine.connect()
            maxdataloadid = self.session.query(func.max(dbsch.sysdataloads.dataload_id)) 
            maxdataloadid_rec = self.session.query(dbsch.sysdataloads).filter_by(dataload_id=maxdataloadid).first()
            self.sysdlrec = None
            if maxdataloadid_rec == None or maxdataloadid_rec.dataload_status != gvar.INPROGRESS :   
                self.sysdlrec = dbsch.sysdataloads(dataload_key=loadkey, dataload_source=src, dataload_status=status,dataload_cycleDate=cycleDate,dataload_importedby=importedby,dataload_comments=comments,dataload_timestamp=datetime.utcnow(), dataload_key_JSON = strJsonVal)
                self.session.add(self.sysdlrec)
                self.session.commit()
                gvar.dataload_id = self.sysdlrec.dataload_id
            else:
                raise TranInprogress("A transaction is already in progress. Please try after sometime.")
        except TranInprogress as e:
            logging.exception("Exception occurred in insert_dataloadkey() method :: " + str(e))
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, loadkey, 'insert_dataloadkey', str(datetime.now())[0:23], ("Exception occurred in insert_dataloadkey - " + str(e)), None)
            logger.error('Action Log Insert Failed: '+ str(e))
            raise e             
        except Exception as e:
            logging.exception("Exception occurred in insert_dataloadkey() method :: " + str(e))
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, loadkey, 'insert_dataloadkey', str(datetime.now())[0:23], ("Exception occurred in insert_dataloadkey - " + str(e)), None)
            logger.error('Action Log Insert Failed: '+ str(e))
            raise e   

    def get_dataloadkey(self, loadkey, cycleDate = ''):
        logging.debug("In get_dataloadkey() method") 
        try:
            maxdataloadid = self.session.query(func.max(dbsch.sysdataloads.dataload_id)) 
            self.sysdlrec = self.session.query(dbsch.sysdataloads).filter_by(dataload_id=maxdataloadid,dataload_key=loadkey,
                                            dataload_cycleDate=cycleDate).first()
        except Exception as e:
            logging.exception("Exception occurred in get_dataloadkey() method :: " + str(e))
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, loadkey, 'get_dataloadkey', str(datetime.now())[0:23], ("Exception occurred in get_dataloadkey - " + str(e)), None)
            logger.error('Data load get key failed: '+ str(e))
            self.sysdlrec = None
     
    def update_dataloadkey(self,status='',statusdetails=''):
        logging.debug("In get_dataloadkey() method") 
        try:
         self.sysdlrec.dataload_status = status
         self.sysdlrec.dataload_statusdetails = statusdetails
         self.session.commit()
        except Exception as e:
            logging.exception("Exception occurred in update_dataloadkey() method :: " + str(e)) 
            logger.error('Data load key update failed: '+ str(e)) 
            raise e  

    # executeSADRD_SP to accept any number of parameters
    def executeSADRD_SP(self, inputParamsList):
        logging.debug("In executeSADRD_SP() method")
        spName = ''
        inputParams = []
        spOutputResult =''

        if len(inputParamsList) > 0:
            spName = inputParamsList[0]
            if len(inputParamsList) > 1:
                for input in inputParamsList[1:]:
                    inputParams.append(input)
            connection = self.engine.raw_connection()
            try:
                cursor = connection.cursor()           
                sql =  """\
                    SET NOCOUNT ON; 
                    DECLARE @RC int;
                    exec @RC = """ + spName +""" ; 
                    SELECT @RC AS rc; 
                    SET NOCOUNT OFF;            
                    """                     
                if '@ReturnCode' in sql:
                    spOutputResult = cursor.execute(sql, inputParams).fetchone()[0]
                else:
                    cursor.execute(sql, inputParams)
                cursor.close()
                connection.commit()
            except Exception as e:
                self.session.rollback()
                logging.exception("Exception occurred in executeSADRD_SP() method :: " + str(e)) 
                self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, 'executeSADRD_SP', 'executeSADRD_SP', str(datetime.now())[0:23], ("Exception occurred in executeSADRD_SP() - " + str(e)), None)
                logger.error('Error occured in executing SP: ' + spName + ';' + str(e))
                raise e
            return spOutputResult

    def executeNIR_SP(self, sp, year, company):
        logging.debug("In executeNIR_SP() method")
        try:
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"])
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
            self.connection = self.engine.connect()
            
            sql_query = "EXEC " + sp + " " + str(year) + ", '" + company +"'"
            df = pd.read_sql_query((sql_query), self.connection)
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "Annual Stmt - Sch D", 'executeNIR_SP()', str(datetime.now())[0:23], "dboperations - executeNIR_SP method executed", None)
            self.session.commit()
        except Exception as e:
            logging.exception("Exception occurred in executeNIR_SP() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, 'Annual Stmt - Sch D', 'executeNIR_SP()', str(datetime.now())[0:23], ("Exception occurred in executeNIR_SP() - " + str(e)), None)
            logger.error('Error occured in executing SP: ' + sp + ';' + str(e))
            raise e
        finally:
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
        return df

    # def executeSADRD_SP(self, spname):
    #     # Call Stored Procedure Statement
    #     connection = self.engine.raw_connection()
    #     try:
    #         cursor = connection.cursor()  
    #      #self.session.execute('SET NOCOUNT ON;')
         
    #         sql =  """\
    #             SET NOCOUNT ON; 
    #             DECLARE @RC int;
    #             exec @RC = """ + spname + """ ; 
    #             SELECT @RC AS rc; 
    #             SET NOCOUNT OFF;            
    #             """                     
    #         cursor.execute(sql)

    #         #results = list(cursor.fetchall())
    #         cursor.close()
    #         connection.commit()
    #      #self.session.execute('SET NOCOUNT OFF;')
            
    #     except Exception as e:
    #         self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, spname, 'executeSADRD_SP', str(datetime.now())[0:23], ("Exception occurred in executeSADRD_SP() - " + str(e)), None)
    #         logger.error('Error occured in executing SP: ' + spname + ';' + str(e))
    #         raise e		        
    #     #return results 
        
    # #Need to change logic of executeSADRD_SP_WithParams to accept any no parameters
    # def executeSADRD_SP_WithParams(self, spname ,param):
    #     # Call Stored Procedure Statement
    #     connection = self.engine.raw_connection()
    #     try:
    #         cursor = connection.cursor()  
    #      #self.session.execute('SET NOCOUNT ON;')
         
    #         sql =  """\
    #             SET NOCOUNT ON; 
    #             DECLARE @RC int;
    #             exec @RC = """ + spname +""" ; 
    #             SELECT @RC AS rc; 
    #             SET NOCOUNT OFF;            
    #             """                     
    #         cursor.execute(sql,param)

    #         #results = list(cursor.fetchall())
    #         cursor.close()
    #         connection.commit()
    #      #self.session.execute('SET NOCOUNT OFF;')
            
    #     except Exception as e:
    #         self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, spname, 'executeSADRD_SP', str(datetime.now())[0:23], ("Exception occurred in executeSADRD_SP() - " + str(e)), None)
    #         logger.error('Error occured in executing SP: ' + spname + ';' + str(e))
    #         raise e		        
    #     #return results 
        
    def executeView_GetDetails(self, importType, rpsType):
        logging.debug("In executeView_GetDetails() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
            self.connection = self.engine.connect()
            if "generateSADRDReport" == importType and rpsType == "rpsCusip":
                viewDetails = pd.read_sql("SELECT * FROM vSADRD_UVCusips", self.connection)                
            elif "generateSADRDReport" == importType and rpsType == "nonrpsCusip":
                viewDetails = pd.read_sql("SELECT * FROM vSADRD_VPACusips", self.connection)
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "executeView_GetDetails", importType, str(datetime.now())[0:23], "dboperations - executeView_GetDetails method executed for " + rpsType, None)
            self.session.commit()
        except Exception as e:
            logging.exception("Exception occurred in executeView_GetDetails() method :: " + str(e)) 
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
            if "generateSADRDReport" == importType:
                logger.error('Exception occured in executing view vSADRD_UVCusips or vSADRD_VPACusips: ' + str(e))
            raise e
        return viewDetails

    def insert_actionLog(self, month, year, userId, module, action, actionDate, comments, dataload_id, showInUI = 0):
        logging.debug("In insert_actionLog() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
            self.connection = self.engine.connect()
            self.actionLogRec = dbsch.ActionLog(Month = month, Year = year, UserID = userId, Module = module, Action = action, ActionDate = actionDate, Comments = comments, Dataload_Id = dataload_id, ShowInUI = showInUI)
            self.session.add(self.actionLogRec)
            self.session.commit()
        except Exception as e:
            self.session.rollback()
            logging.exception("Exception occurred in insert_actionLog() method :: " + str(e)) 
            logger.error('Action Log Insert Failed: '+ str(e))
            raise e

    def get_actionLog(self):
        logging.debug("In get_actionLog() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
            self.connection = self.engine.connect()
            self.session = Session(self.engine)
            ResultProxy = self.session.query(dbsch.ActionLog).filter_by(ShowInUI=1)
            actionLog = ResultProxy.all()
        except Exception as e:
            logging.exception("Exception occurred in get_actionLog() method :: " + str(e)) 
            logger.error('Error occured in get_actionLog(): ' + str(e))
            raise e
        return actionLog

    def GetAllUsers(self):
        logging.debug("In GetAllUsers() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
            self.connection = self.engine.connect()
            self.session.commit()
            ResultProxy = self.session.query(dbsch.Users)
            self.usersRec = ResultProxy.all()
        except Exception as e:
            logging.exception("Exception occurred in GetAllUsers() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "GetAllUsers", 'GetAllUsers', str(datetime.now())[0:23], ("Exception occurred in GetAllUsers() - " + str(e)), None)
            logger.error('Error occured in GetAllUsers(): ' + str(e))
            raise e
        return self.usersRec

    def GetAllRoles(self):
        logging.debug("In GetAllRoles() method")
        try:
            ResultProxy = self.session.query(dbsch.Roles)
            self.rolesRec = ResultProxy.all()
        except Exception as e:
            logging.exception("Exception occurred in GetAllRoles() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "GetAllRoles", 'GetAllRoles', str(datetime.now())[0:23], ("Exception occurred in GetAllRoles() - " + str(e)), None)
            logger.error('Error occured in GetAllRoles(): ' + str(e))
            raise e
        return self.rolesRec

    def UpdateUser(self, input):

        logging.debug("In UpdateUser() method")

        roleId = ''
        status = "Exists"
        networkIdExists = False
        IsActive = True if input['isActive'] == "true" else False

        connection = self.engine.raw_connection()

        try:
            for role in self.rolesRec:
                if role.Type == input['Type']:
                    roleId = role.RoleID
                    break    
            for user in self.usersRec:
                if user.NetworkId == input['NetworkId']:
                    networkIdExists = True
                    break
            logging.debug("User Action :: " + input['userAction'] + " user")
            if not networkIdExists:
                self.userDetailsRec = dbsch.Users(Name = input['Name'], NetworkId = input['NetworkId'], RoleId = roleId, isActive = 1 if IsActive else 0, Email = input['Email'])
                self.session.add(self.userDetailsRec)
                self.session.commit()
                status = "Success"
                self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateUser", "Update User - User Name : " + input['Name'], str(datetime.now())[0:23], ("User added successfully"), None) 
            else:
                if "update" == input['userAction']:
                    cursor = connection.cursor()
                    cursor.execute('UPDATE SADRD_Users SET RoleId = ?, isActive = ? WHERE NetworkId = ?', (roleId, (1 if IsActive else 0), input['NetworkId']))
                    cursor.close()
                    connection.commit()
                    status = "Success"
                    self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateUser", "Update User - User Name : " + input['Name'], str(datetime.now())[0:23], ("User updated successfully"), None) 
                else:
                    self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateUser", "Update User - User Name : " + input['Name'], str(datetime.now())[0:23], ("User already exists"), None)             
            logging.debug("User Action :: " + input['userAction'] + " user - " + status)
        except Exception as e:
            logging.exception("Exception occurred in UpdateUser() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateUser", 'Update User', str(datetime.now())[0:23], ("Exception occurred in UpdateUser() - " + str(e)), None)
            logger.error('Update User Failed: '+ str(e))
            raise e
            
        return status

    def SadrdSysSettings(self):
        logging.debug("In SadrdSysSettings() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
            self.connection = self.engine.connect()
            self.session = Session(self.engine)
            ResultProxy = self.session.query(dbsch.Settings)
            self.settingsRec = ResultProxy.all()
        except Exception as e:
            logging.exception("Exception occurred in SadrdSysSettings() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "SadrdSysSettings", 'SadrdSysSettings', str(datetime.now())[0:23], ("Exception occurred in SadrdSysSettings() - " + str(e)), None)
            logger.error('Error occured in SadrdSysSettings(): ' + str(e))
            raise e
        return self.settingsRec
    
    def SADRD_Sys_Message(self):
        logging.debug("In SADRD_Sys_Message() method")
        try:
            ResultProxy = self.session.query(dbsch.Sys_Message)
            self.settingsRec = ResultProxy.all()
        except Exception as e:
            logging.exception("Exception occurred in SADRD_Sys_Message() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "SADRD_Sys_Message", 'SadrdSysSettings', str(datetime.now())[0:23], ("Exception occurred in SadrdSysSettings() - " + str(e)), None)
            logger.error('Error occured in SADRD_Sys_Message(): ' + str(e))
            raise e
        return self.settingsRec

    def UpdateFilePath(self, request):
        logging.debug("In UpdateFilePath() method")
        connection = self.engine.raw_connection()
        try:
            cursor = connection.cursor()
            sql = "UPDATE SADRD_Sys_Settings SET settingValue = '" + request['NewFileLocation'] + "' WHERE settingName = 'ServerFolderPath'"
            cursor.execute(sql)
            cursor.close()
            connection.commit()
            status = "Success"
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateFilePath", 'UpdateFilePath', str(datetime.now())[0:23], "File Path updated successfully", None)
        except Exception as e:
            logging.exception("Exception occurred in UpdateFilePath() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateFilePath", 'UpdateFilePath', str(datetime.now())[0:23], ("Exception occurred in UpdateFilePath() - " + str(e)), None)
            logger.error('Error occured in UpdateFilePath(): ' + str(e))
            raise e
        return status

    def CloseTaxYear(self, spName, sadrdYear):

        logging.debug("In CloseTaxYear() method")

        msgCloseTaxYear = ""
        connection = self.engine.raw_connection()

        try:
            cursor = connection.cursor()
            sql =  """\
                SET NOCOUNT ON; 
                DECLARE @RC int;
                exec @RC = sp_SADRD_INSERT_HistoryTables ; 
                SELECT @RC AS rc; 
                SET NOCOUNT OFF;            
                """                     
            cursor.execute(sql)
            cursor.close()
            connection.commit()
            dataCloseTaxYear = [x for x in gvar.sadrd_ErrMessages if x.MessageNumber == 'E019']
            msgCloseTaxYear = dataCloseTaxYear[0].Message.replace('[YYYY]', sadrdYear)   
        except Exception as e:
            logging.exception("Exception occurred in CloseTaxYear() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, spName, 'CloseTaxYear', str(datetime.now())[0:23], ("Exception occurred in CloseTaxYear() - " + str(e)), None)
            logger.error('Exception occurred in CloseTaxYear() :: ' + spName + ' ; ' + str(e))
            raise e		   

        return msgCloseTaxYear

    def UpdateSettingsData(self, input):

        logging.debug("In UpdateSettingsData() method")

        status = "Exists"
        settingRecordExists = False
        
        connection = self.engine.raw_connection()
        
        try:
            for setting in gvar.sadrd_settings:
                if setting.settingName == input['settingName'] and setting.settingValue == input['settingValue']:
                    settingRecordExists = True
                    break
            logging.debug("User Action :: " + input['userAction'] + " setting")
            if "add" == input['userAction'] and not settingRecordExists:
                self.settingsRec = dbsch.Settings(settingName = input['settingName'], settingValue = input['settingValue'], ShowInUI = 1, Description = input['Description'], DataType='')
                self.session.add(self.settingsRec)
                self.session.commit()
                status = "Success"
            elif "update" == input['userAction']:
                cursor = connection.cursor()
                if settingRecordExists and (input['settingValue'] == input['oldSettingValue']) and (not input['Description'] == input['oldDescription']):
                    cursor.execute('UPDATE SADRD_Sys_Settings SET Description = ? WHERE settingName = ? and settingValue = ?', (input['Description'], input['settingName'], input['oldSettingValue']))
                    cursor.close()
                    connection.commit()
                    status = "Success"
                elif (not settingRecordExists and (not input['settingValue'] == input['oldSettingValue'])):
                    cursor.execute('UPDATE SADRD_Sys_Settings SET settingValue = ?, Description = ? WHERE settingName = ? and settingValue = ?', (input['settingValue'], input['Description'], input['settingName'], input['oldSettingValue']))
                    cursor.close()
                    connection.commit()
                    status = "Success"
            logging.debug("User Action :: " + input['userAction'] + " setting - " + status)
        except Exception as e:
            logging.exception("Exception occurred in UpdateSettingsData() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateSettingsData", 'UpdateSettingsData', str(datetime.now())[0:23], ("Exception occurred in UpdateSettingsData() - " + str(e)), None)
            logger.error('Exception occurred in UpdateSettingsData() :: '+ str(e))
            raise e
    
        return status 

    def SadrdAdmScheduleE(self):
        logging.debug("In SadrdAdmScheduleE() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
            self.connection = self.engine.connect()
            self.session = Session(self.engine)
            ResultProxy = self.session.query(dbsch.Adm_ScheduleE)
            self.admSchERec = ResultProxy.all()
        except Exception as e:
            logging.exception("Exception occurred in SadrdAdmScheduleE() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "SadrdAdmScheduleE", 'SadrdAdmScheduleE', str(datetime.now())[0:23], ("Exception occurred in SadrdAdmScheduleE() - " + str(e)), None)
            logger.error('Error occured in SadrdAdmScheduleE(): ' + str(e))
            raise e
        return self.admSchERec

    def UpdateAdmScheduleEData(self, input):

        logging.debug("In UpdateAdmScheduleEData() method")

        status = "Exists"
        schERecordExists = False
        connection = self.engine.raw_connection()
                
        try:
            if "delete" == input['userAction']:
                cursor = connection.cursor()
                cursor.execute('DELETE FROM SADRD_Adm_ScheduleE WHERE Cusip = ? and CusipName = ?', (input['Cusip'], input['CusipName']))
                cursor.close()
                connection.commit()
                status = "Success"
            else:
                for schERec in gvar.scheduleEList:
                    if schERec.Cusip == input['Cusip']:
                        if ("add" == input['userAction']) or ("update" == input['userAction'] and schERec.CusipName == input['CusipName']):
                            schERecordExists = True
                            break
                logging.debug("User Action :: " + input['userAction'] + " AdmScheduleE")
                if not schERecordExists:
                    if "add" == input['userAction']:
                        self.admSchERec = dbsch.Adm_ScheduleE(Cusip = input['Cusip'], CusipName = input['CusipName'])
                        self.session.add(self.admSchERec)
                        self.session.commit()
                        status = "Success"
                    else:
                        cursor = connection.cursor()
                        cursor.execute('UPDATE SADRD_Adm_ScheduleE SET Cusip = ?, CusipName = ? WHERE Cusip = ? and CusipName = ?', (input['Cusip'], input['CusipName'], input['Cusip'], input['oldCusipName']))
                        cursor.close()
                        connection.commit()
                        status = "Success"
            logging.debug("User Action :: " + input['userAction'] + " AdmScheduleE - " + status)
        except Exception as e:
            logging.exception("Exception occurred in UpdateAdmScheduleEData() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateAdmScheduleEData", 'UpdateAdmScheduleEData', str(datetime.now())[0:23], ("Exception occurred in UpdateAdmScheduleEData() - " + str(e)), None)
            logger.error('Exception occurred in UpdateAdmScheduleEData() :: ' + str(e))
            raise e

        return status
    
    def BuildErrorMessage(self, errorMessage):
        logging.debug("In BuildErrorMessage() method")
        errMessageComplete = ''
        try:
            errorMessageList = errorMessage.split(';')
            errorMessageListCount = len(errorMessageList)
            for i in range(errorMessageListCount):
                if  errorMessageList[i] != "":
                    errorMessageIndividualList = errorMessageList[i].split(',')
                    datatErrMessage = [x for x in gvar.sadrd_ErrMessages if x.MessageNumber == errorMessageIndividualList[0].strip()]
                    if errorMessageIndividualList[0].strip() == "E018":
                        errMessage = datatErrMessage[0].Message.replace('[Number of files loaded]', errorMessageIndividualList[1].strip())
                    elif errorMessageIndividualList[0].strip() == "E034":
                        errMessage = datatErrMessage[0].Message                       
                    elif errorMessageIndividualList[0].strip() == "E032":
                        errMessage = datatErrMessage[0].Message.replace('[Insert Quarter Name Here]', errorMessageIndividualList[1].strip()).replace(' |', ',') + " " + (datatErrMessage[0].Action)
                    else:
                        errMessage = datatErrMessage[0].Message.replace('[insert file name here]', errorMessageIndividualList[1].strip()).replace('[insert tab name here]', errorMessageIndividualList[2].strip()) + " " + datatErrMessage[0].Action                        
                    errMessageComplete = errMessage if errMessageComplete == '' else (errMessageComplete + "; " + errMessage)
        except Exception as e:
            self.session.rollback()
            logging.exception("Exception occurred in BuildErrorMessage() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "dboperations - BuildErrorMessage", 'BuildErrorMessage', str(datetime.now())[0:23], ("Exception occurred in BuildErrorMessage() - " + str(e)), None)
            logger.error('Exception occurred in BuildErrorMessage() :: ' + str(e))
            raise e
        return errMessageComplete 

    def GetQualPctOverrideData(self):
        logging.debug("In GetQualPctOverrideData() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
            self.connection = self.engine.connect()
            self.session = Session(self.engine)
            ResultProxy = self.session.query(dbsch.CusipQualOvr)
            self.qualPctRec = ResultProxy.all()
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "GetQualPctOverrideData()", 'GetQualPctOverrideData', str(datetime.now())[0:23], ("dboperations - GetQualPctOverrideData() method executed"), None)
        except Exception as e:
            logging.exception("Exception occurred in GetQualPctOverrideData() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "GetQualPctOverrideData", 'GetQualPctOverrideData', str(datetime.now())[0:23], ("Exception occurred in GetQualPctOverrideData() - " + str(e)), None)
            logger.error('Error occured in GetQualPctOverrideData(): ' + str(e))
            raise e
        return self.qualPctRec

    def UpdateQualPctData(self, input):

        logging.debug("In UpdateQualPctData() method")

        status = "Exists"
        qualPctRecExists = False
        connection = self.engine.raw_connection()

        try:
            if "delete" == input['userAction']:
                cursor = connection.cursor()
                cursor.execute('DELETE FROM SADRD_Srctemp_CusipQualOvr WHERE Year = ? and Company = ? and Cusip = ?', (input['Year'], input['Company'], input['Cusip']))
                cursor.close()
                connection.commit()
                status = "Success"
            else:
                for qualPctRec in gvar.qualPctList:
                    if str(qualPctRec.Year) == input['Year'] and qualPctRec.Company == input['Company'] and qualPctRec.Cusip == input['Cusip']:
                        if not (input['Company'] == input['oldCompany'] and input['Cusip'] == input['oldCusip']):
                            qualPctRecExists = True
                            break
                logging.debug("User Action :: " + input['userAction'] + " QualPctOverride")
                if not qualPctRecExists:
                    if "add" == input['userAction']:
                        self.qualPctRec = dbsch.CusipQualOvr(Year = input['Year'], Company = input['Company'], Cusip = input['Cusip'], QualPct = input['QualPct'])
                        self.session.add(self.qualPctRec)
                        self.session.commit()
                        status = "Success"
                    else:
                        cursor = connection.cursor()
                        cursor.execute('UPDATE SADRD_Srctemp_CusipQualOvr SET Company = ?, Cusip = ?, QualPct = ? WHERE Year = ? and Company = ? and Cusip = ?', (input['Company'], input['Cusip'], input['QualPct'], input['Year'], input['oldCompany'], input['oldCusip']))
                        cursor.close()
                        connection.commit()
                        status = "Success"
            logging.debug("User Action :: " + input['userAction'] + " QualPctOverride - " + status)
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateReportStatus()", 'UpdateQualPctData', str(datetime.now())[0:23], ("dboperations - UpdateQualPctData() - " + input['userAction'] + " method executed"), None)           
        except Exception as e:
            logging.exception("Exception occurred in UpdateQualPctData() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "UpdateQualPctData()", 'UpdateQualPctData', str(datetime.now())[0:23], ("Exception occurred in dboperations - UpdateQualPctData() - " + str(e)), None)
            logger.error('Exception occurred in UpdateQualPctData() :: '+ str(e))
            raise e

        return status
    
    def UpdateReportStatus(self, inputValue, settingName, moduleName):
        logging.debug("In UpdateReportStatus() method")
        try:
            connection = self.engine.raw_connection()
            cursor = connection.cursor()
            sql = "UPDATE SADRD_Sys_Settings SET settingValue = '" + inputValue + "' WHERE settingName = '" + settingName + "'"
            cursor.execute(sql)
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, moduleName, 'UpdateReportStatus()', str(datetime.now())[0:23], ("dboperations - UpdateReportStatus() method executed"), None)
            cursor.close()
            connection.commit()
        except Exception as e:
            self.session.rollback()
            logging.exception("Exception occurred in UpdateReportStatus() method :: " + str(e)) 
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, moduleName, 'UpdateReportStatus()', str(datetime.now())[0:23], ("Exception occurred in dboperations - UpdateReportStatus() - " + str(e)), None)
            logger.error('Exception occurred in UpdateReportStatus() :: '+ str(e))
            raise e
        
    def GetVPAData(self, importType, year, dataType, actionType):
        logging.debug("In GetVPAData() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
            self.connection = self.engine.connect()
            if dataType == 'GL':
                if actionType == 'CtrlTot':
                    vpaData = pd.read_sql(("SELECT [Year], [Company], Sum(Amount) AS [Amount] FROM GL WHERE Year = " + year + " GROUP BY [Year], [Company]"), self.connection)
                else:
                    vpaData = pd.read_sql(("SELECT [Month], [Year], [Company], [SeparateAccount], [GLAccount], [Amount], [EssbaseFund] FROM GL WHERE Year = " + year), self.connection)
            elif dataType == 'SeparateAccounts':
                vpaData = pd.read_sql("SELECT [Company], [SeparateAccount], [BusinessUnit] FROM SeparateAccounts", self.connection)
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "GetVPAData()", importType, str(datetime.now())[0:23], "dboperations - GetVPAData method executed", None)
            self.session.commit()
        except Exception as e:
            logging.exception("Exception occurred in GetVPAData() method :: " + str(e)) 
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "GetVPAData()", 'GetVPAData', str(datetime.now())[0:23], ("Exception occurred in dboperations - GetVPAData() - " + str(e)), None)
            logger.error('Exception occured in GetVPAData() method :: ' + str(e))
            raise e
        return vpaData
    
    def InsertControlTotals(self, importType, year, company, amount):
        logging.debug("In InsertControlTotals() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
            connection = self.engine.raw_connection()
            cursor = connection.cursor()
            cursor.execute("INSERT INTO SADRD_FactsTemp_CntrlTotals_Input VALUES(?, ?, ?, ?, ?, ?, ?, ?, ?)", ('VPA', 'GL', 'GL', company, int(year), amount, 0, 0, 0))
            cursor.close()
            connection.commit()
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "InsertControlTotals()", importType, str(datetime.now())[0:23], "dboperations - InsertControlTotals method executed", None)
            self.session.commit()
        except Exception as e:
            logging.exception("Exception occurred in InsertControlTotals() method :: " + str(e)) 
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
            logger.error('Exception occured in InsertControlTotals() method :: ' + str(e))
            raise e
        
