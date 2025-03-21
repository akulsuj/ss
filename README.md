import logging
import os
import shutil
from datetime import datetime

import pandas as pd
from Entities.Customentities import ApihomeResp
from Services import CustomException

import Services.SADRD_Dataparser   as sdp
import Services.fileoperations as fiops
import Services.dboperations   as dbops
import datetime
import globalvars as gvar
import urllib

#app.config["DEBUG"] = True
class Emptyfoldererror(Exception):
    pass

def parentparser(serverInputFilesByAction, Inputdirpath, importType, Year, copyFlag='No'):

    logging.debug("In parentparser() method")

    try:
        response = ApihomeResp()

        errInputFilesMessage = ''
        gvar.fileErrorMessages = "" 
        gvar.filesLoadedCount = 0
        dbops_obj = dbops.dboperations()
        gvar.sadrd_settings = dbops_obj.SadrdSysSettings()   
        gvar.sadrd_ErrMessages = dbops_obj.SADRD_Sys_Message()

        lscompanies = [x for x in gvar.sadrd_settings if x.settingName == 'Valid_Company']
        partTypes = [x for x in gvar.sadrd_settings if x.settingName == 'IncludePartType_Funds']
        
        logging.debug("Import Type :: " + importType)

        if importType == 'Annual Stmt - Sch D' or importType == "QualPctFTC" or importType == "FTCGrossup":

            lsinpfiles = [] 

            if importType == 'Annual Stmt - Sch D':
                for company in lscompanies:
                    for partType in partTypes:
                        sdp.parseAnnualStmntFile(company.settingValue, partType.settingValue, importType, Year, "Secondary Validation")

            elif importType == "QualPctFTC" or importType == "FTCGrossup":
                lsinpfiles = fiops.DownloadServerFilesToLoad(serverInputFilesByAction, Inputdirpath, importType, Year)
            
                for inpfile in lsinpfiles:
                    if(not inpfile.startswith(Inputdirpath+'\\~$')):
                        if importType == 'QualPctFTC':
                            sdp.parseCusipQualFTCFile(importType, inpfile, Year, "SecondaryValidation")
                        elif importType == 'FTCGrossup':
                            sdp.parseJHFundsFTCGrossupFile(importType, inpfile, Year, "SecondaryValidation")
                        continue

            if gvar.fileErrorMessages != "":
                errInputFilesMessage = dbops_obj.BuildErrorMessage(gvar.fileErrorMessages)
                raise CustomException.FileValidationException(errInputFilesMessage)
            else:
                if importType == 'Annual Stmt - Sch D' or importType == "QualPctFTC" or importType == "FTCGrossup":  
                    if importType == 'Annual Stmt - Sch D':
                        dbops_obj.truncatetable('SADRD_Srctemp_SchDAllPartsdata', 'Yes')
                        dbops_obj.truncatetable('SADRD_SrcStaging_SchDAllPartsdata', 'Yes')    
                        dbops_obj.truncatetable('SADRD_FactsTemp_CntrlTotals_Input', "nonQualFTC")  
                        dbops_obj.truncatetable('SADRD_Factstemp_Exception', "Sch D Part")        
                    elif importType == 'QualPctFTC':
                        dbops_obj.truncatetable('SADRD_Srctemp_CusipQualFTC', 'Yes')
                        dbops_obj.truncatetable('SADRD_SrcStaging_CusipQualFTC', 'Yes') 
                        dbops_obj.truncatetable('SADRD_FactsTemp_CntrlTotals_Input', "QualFTC")
                        dbops_obj.truncatetable('SADRD_Factstemp_Exception', "QualPctFTC")   
                    elif importType == 'FTCGrossup':
                        dbops_obj.truncatetable('SADRD_Srctemp_BankGrossUpPct', 'Yes')
                        dbops_obj.truncatetable('SADRD_SrcStaging_BankGrossUpPct', 'Yes') 
                        dbops_obj.truncatetable('SADRD_FactsTemp_CntrlTotals_Input', "FTCGrossup")
                        dbops_obj.truncatetable('SADRD_Factstemp_Exception', "FTCGrossup")  


                    if importType == 'Annual Stmt - Sch D':
                        for company in lscompanies:
                            for partType in partTypes:
                                sdp.parseAnnualStmntFile(company.settingValue, partType.settingValue, importType, Year, "")
                    else:
                        for inpfile in lsinpfiles:
                            if(not inpfile.startswith(Inputdirpath+'\\~$')):
                                if importType == 'QualPctFTC':
                                    sdp.parseCusipQualFTCFile(importType, inpfile, Year, "")
                                elif importType == 'FTCGrossup':
                                    sdp.parseJHFundsFTCGrossupFile(importType, inpfile, Year, "")
                                continue
                    if gvar.fileErrorMessages != "" and 'E026' not in gvar.fileErrorMessages:
                        errInputFilesMessage = dbops_obj.BuildErrorMessage(gvar.fileErrorMessages)
                        raise CustomException.FileValidationException(errInputFilesMessage)

                if importType == 'Annual Stmt - Sch D':
                    successMessage = 'E034' + "," 
                else: 
                    successMessage = 'E018' + "," + str(gvar.filesLoadedCount) + "," + ""

                successInputFilesMessage = dbops_obj.BuildErrorMessage(successMessage)

                if successInputFilesMessage != "" and importType == 'Annual Stmt - Sch D':
                    inputParamsDict = []
                    inputParamsDict.append('sp_SADRD_INSERT_SchD_Exceptions')
                    dbops_obj.executeSADRD_SP(inputParamsDict)
                    dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_SchD_Exceptions", gvar.dataload_id)
                    
                    inputParamsDict = []
                    inputParamsDict.append('[sp_SADRD_INSERT_Srctemp_I&Icusip_DivAmts]')
                    dbops_obj.executeSADRD_SP(inputParamsDict)
                    dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_Srctemp_I&Icusip_DivAmts", gvar.dataload_id)

                if successInputFilesMessage != "" and importType == 'QualPctFTC':
                    inputParamsDict = []
                    inputParamsDict.append('sp_SADRD_INSERT_CusipQualFTC')
                    dbops_obj.executeSADRD_SP(inputParamsDict)
                    
                    dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_CusipQualFTC", gvar.dataload_id)
                    
                    inputParamsDict = []
                    inputParamsDict.append('sp_SADRD_INSERT_CtrlTotCusipQualFTC')
                    dbops_obj.executeSADRD_SP(inputParamsDict)

                    dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_CtrlTotCusipQualFTC", gvar.dataload_id)

                if successInputFilesMessage != "" and importType == 'FTCGrossup':
                    inputParamsDict = []
                    inputParamsDict.append('sp_SADRD_INSERT_BankGrossUpPct @fileName=?, @updateControlTotalsFlag=?, @ReturnCode=?')
                    inputParamsDict.append('')
                    inputParamsDict.append('Y')
                    inputParamsDict.append('Y')
                    spOutputResult = dbops_obj.executeSADRD_SP(inputParamsDict)
                    dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_BankGrossUpPct", gvar.dataload_id)

                    inputParamsDict = []
                    inputParamsDict.append('sp_SADRD_INSERT_CtrlTotBankGrossUpPct')
                    dbops_obj.executeSADRD_SP(inputParamsDict)
                    dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_CtrlTotBankGrossUpPct", gvar.dataload_id)

                    if gvar.fileErrorMessages != "" and 'E026' in gvar.fileErrorMessages:
                        errInputFilesMessage = dbops_obj.BuildErrorMessage(gvar.fileErrorMessages)
                        raise CustomException.FileValidationException(errInputFilesMessage) 

                response.status = "Success"
                response.message = importType + " - " + successInputFilesMessage

                dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], (importType + " File loaded succesfully"), None)
                dbops.logger.info(successInputFilesMessage)           
        
        elif importType == 'generateSADRDReport':
            isRefreshUVAndVPA = [x for x in gvar.sadrd_settings if x.settingName == 'IsRefreshUVAndVPA'][0].settingValue
            if isRefreshUVAndVPA == 'Y':
                cusipType = "rpsCusip"
                gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["UV_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["UV_DATABASE_NAME"] + gvar.gconfig["UV_DATABASE_AUTH_STRING"])
                dfRpsFundCusipDetails = sdp.executeView_GetDetails(importType, cusipType)
                dfRpsFundCusipDetails = dfRpsFundCusipDetails.rename({'RpsFundCompany': 'Company'}, axis=1) 
                dfRpsFundCusipDetails.insert(4, 'Source', 'UV')

                cusipType = "nonrpsCusip"
                gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["VPA_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["VPA_DATABASE_NAME"] + gvar.gconfig["VPA_DATABASE_AUTH_STRING"])
                dfNonRPSCusips = sdp.executeView_GetDetails(importType, cusipType)
                dfNonRPSCusips = dfNonRPSCusips.rename({'EssbaseFund': 'SA Fund'}, axis=1)
                dfNonRPSCusips.insert(4, 'Source', 'VPA')            
                dfCusipFinal = pd.concat([dfRpsFundCusipDetails, dfNonRPSCusips])
                
                sdp.Load_FundCusipDetails(importType, dfCusipFinal, Year)
                dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser - Load_FundCusipDetails', str(datetime.datetime.now())[0:23], (importType + " Data loaded succesfully from VPA and UV databases succesfully"), gvar.dataload_id)

                gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["VPA_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["VPA_DATABASE_NAME"] + gvar.gconfig["VPA_DATABASE_AUTH_STRING"])
                sdp.LoadVPADataToSADRD(importType, Year, 'GL')
                dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser - LoadVPADataToSADRD', str(datetime.datetime.now())[0:23], (importType + " Data loaded to SADRD_GL succesfully"), gvar.dataload_id)
                
                inputParamsDict = []
                inputParamsDict.append('[sp_SADRD_UPDATE_CtrlTotGL]')
                dbops_obj.executeSADRD_SP(inputParamsDict)
                dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, 'parentparser', importType, str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_UPDATE_CtrlTotGL", None)

                gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["VPA_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["VPA_DATABASE_NAME"] + gvar.gconfig["VPA_DATABASE_AUTH_STRING"])
                sdp.LoadVPADataToSADRD(importType, Year, 'SeparateAccounts')
                dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, 'parentparser', importType, str(datetime.datetime.now())[0:23], (importType + " LoadVPADataToSADRD - Data loaded to SADRD_SeparateAccounts succesfully"), gvar.dataload_id)

            inputParamsDict = []
            inputParamsDict.append('[sp_SADRD_INSERT_Srctemp_AllPartsCusip_GLAmt]')
            dbops_obj.executeSADRD_SP(inputParamsDict)
            dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, 'parentparser', importType, str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_Srctemp_AllPartsCusip_GLAmt", None)
            
            inputParamsDict = []
            inputParamsDict.append('[sp_SADRD_INSERT_GLCusips_GLAmt]')
            dbops_obj.executeSADRD_SP(inputParamsDict)
            dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser - executeSADRD_SP', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_GLCusips_GLAmt", None)
            
            # inputParamsDict = []
            # inputParamsDict.append('[sp_SADRD_INSERT_Srctemp_I&Icusip_DivAmts]')
            # dbops_obj.executeSADRD_SP(inputParamsDict)
            # dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser - generateSADRDReport', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_Srctemp_I&Icusip_DivAmts", gvar.dataload_id)

            inputParamsDict = []
            inputParamsDict.append('[sp_SADRD_INSERT_TaxData] @sadrdYearFromUI=?')
            inputParamsDict.append(Year)
            dbops_obj.executeSADRD_SP(inputParamsDict)
            dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser - executeSADRD_SP', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_INSERT_TaxData", None)
            
            errMessage = 'E014' + "," + "" + "," + ""
            errInputFilesMessage = dbops_obj.BuildErrorMessage(errMessage)

            response.status = "Success"
            response.message = errInputFilesMessage
            #update settings table
    except CustomException.FileValidationException as fe:
        response.status = 'Failure'
        response.message = str(fe)
        logging.exception("Exception occurred in parentparser() method :: " + str(fe))
        dbops.logger.error('Parent parser failed loading ' + importType + str(fe))
        gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
        dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], ("Exception occurred in parentparser - " + str(fe)), None)
    except Exception as e:
        logging.exception("Exception occurred in parentparser() method :: " + str(e))
        dbops.logger.error('Parent parser failed loading ' + importType + str(e))
        gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
        dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, importType, 'parentparser', str(datetime.datetime.now())[0:23], ("Exception occurred in parentparser - " + str(e)), None)
        raise e 

    return response

def copyFileToOutputFolder(Inputdirpath, inpfile, copyFlag='No'):
    logging.debug("In copyFileToOutputFolder() method")
    try:
        path, filename = os.path.split(inpfile)
        fname, ext = filename.split('.')
        if copyFlag.lower() == 'move'.lower():
            shutil.move(os.path.join(path, os.path.basename(inpfile)),
                   os.path.join(Inputdirpath + 'Processed', os.path.basename(inpfile)))
        elif copyFlag.lower() == 'Yes'.lower():
            cpypath = os.path.join(Inputdirpath + 'Processed', datetime.datetime.today().strftime('%Y-%m-%d'))
            if not os.path.exists(cpypath):
                os.makedirs(cpypath)
            shutil.copyfile(os.path.join(path, os.path.basename(inpfile)), os.path.join(cpypath, fname + '_' + datetime.datetime.today().strftime("%d-%b-%Y-%H%M%S") + '.' + ext))
    except Exception as e:
        logging.exception("Exception occurred in copyFileToOutputFolder() method :: " + str(e)) 
        dbops.logger.error('Parser failed copying file to output folder ' + str(e))
        raise e
