import json
import os
import shutil
from tkinter import FIRST, LAST
from turtle import left

from sqlalchemy import true
import globalvars as gvar
import logging, urllib
import datetime
import pandas as pd
from pathlib import Path
from db import db

from flask import Flask
import Services.dboperations   as dbops
from Entities.Customentities import ApihomeResp

cliapp = Flask(__name__)
if cliapp.config["ENV"] == "production":
    cliapp.config.from_object("config.ProductionConfig")
elif cliapp.config["ENV"] == "testing":
    cliapp.config.from_object("config.TestingConfig")
elif cliapp.config["ENV"] == "development":    
    cliapp.config.from_object("config.DevelopmentConfig")
else:
    cliapp.config.from_object("config.LocalConfig")

gvar.user_id = 'thulapr'
settingsAddOrUpdate ='added'
strMsg = 'Clicked on Settings tab in Admin page. Settings data [AddOrUpdate] successfully.'
up = strMsg.replace('[AddOrUpdate]', settingsAddOrUpdate)
up1 =settingsAddOrUpdate

cors = json.loads(cliapp.config["CORS_ORIGINS"])[cliapp.config["ENV"]]

gvar.gconfig = cliapp.config
logging.basicConfig(level=cliapp.config["DEBUG"])
gvar.init()

gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
cliapp.config['SQLALCHEMY_DATABASE_URI'] = gvar.gconfig["SQLALCHEMYODBC"] % gvar.sqlconfig
cliapp.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
cliapp.config['PROPAGATE_EXCEPTIONS'] = True
with cliapp.app_context():
    db.init_app(cliapp)



#gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";"+"SERVER="+gvar.gconfig["SERVER"]+";"+"DATABASE="+gvar.gconfig["DATABASE"]+";"+"UID="+gvar.gconfig["UID"]+";"+"PWD="+gvar.gconfig["PWD"]+";")
#gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";"+"SERVER="+gvar.gconfig["VPA_DATABASE_SERVER"]+";"+"DATABASE="+gvar.gconfig["VPA_DATABASE_NAME"]+";"+"Encrypt=yes;TrustServerCertificate=no;Authentication=ActiveDirectoryIntegrated")

@cliapp.route('/cliapi', methods=['GET'])
def cliapi():

    try:
     #print("Start time=" , datetime.now().strftime("%H:%M:%S"))
     print("Start time=", datetime.datetime.now())
     import Services.dboperations   as dbops
     from Services.parentparser import parentparser
    
     '''
        myfile = ("//mfcgd.com/aznadfs/US/JHFX01DEV02/develop/SADRD_InputFiles/testLoad/testload_secondValidation/testFilexls.xlsx", "r+")
        if is_open(myfile[0]) == True:
            openstatus =true
        else:
            openstatus =False
        print(openstatus)
     '''
     #actionname = 'Annual Stmt - Sch D'
     #actionname = 'ScheduleD'
     actionname = 'generateSADRDReport'
     #actionname = 'QualPctFTC'
     #actionname = 'FTCGrossup'
     serverInputFilesFolder = json.loads(cliapp.config["SADRD_SERVER_FOLDER"])
     Year = '2022'
     '''
     dbops_obj = dbops.dboperations()
     dbops_obj.UpdateUser("Prakash Thulabandala", "thulapr", 1, "Admin",
                                                    "PTHULABANDALA_1@jhancock.com", "update")
     
     if actionname == 'FTCGrossup':
        subFolder= "FTCGrossup"
    '''
     serverInputFilesByAction = Path.cwd().joinpath(str(serverInputFilesFolder['serverFolderPath']),"")
     Inputdirpath = Path.cwd().joinpath('Data',actionname)


     '''
     for (row, sadrdSysSetting) in enumerate(gvar.sadrd_settings):
         if sadrdSysSetting.settingName == "SADRD_Year":
              year = sadrdSysSetting.settingValue
              break

     importType = "generateSADRDReport"
     
     parentparser(str(serverInputFilesByAction), str(Inputdirpath), importType, year, 'No')
    '''




     parentparser(str(serverInputFilesByAction), str(Inputdirpath),actionname, Year, 'Yes')
     #parseCusipQualFTCFile('CusipQualFTC', '\\\\mfcgd.com\\aznadfs\\US\\JHFX01DEV02\\develop\\SADRD_InputFiles\\2020_RPS_SADRDCusips - JH 20221005.xlsx',  Year)
     print("End time=", datetime.datetime.now())
     gvar.apihome_Respobj.message="CLI action completed"
     gvar.apiResp.setResponse(gvar.apihome_Respobj)
    
     return gvar.apiResp.getResponse()
 
    except Exception as e:
     dbops.logger.error('Parser failed ' + str(e))    

if __name__ == '__main__':
   cliapp.run()

'''
def is_open(file_name):
    if os.path.exists(file_name):
        try:
            os.rename(file_name, file_name) #can't rename an open file so an error will be thrown
            return False
        except:
            return True
    raise NameError
'''
'''  
def parseCusipQualFTCFile(actionname, inpfile, UIYear):
    try:
        response = ApihomeResp()
        dbops_obj = dbops.dboperations()
        datatb = pd.DataFrame()       
        file_name = os.path.basename(inpfile)
        file_name_1 = file_name.split(".")[0]

        year = file_name_1.split("_")[0]
    
        listCompany =[]
        for (row, sadrdSysSetting) in enumerate(gvar.sadrd_settings):
            if sadrdSysSetting.settingName == "Valid_Company":
                listCompany.append(sadrdSysSetting.settingValue)

        if file_name_1 != None and "JHFunds" in file_name_1:
            fileType = 'JHFunds'
        elif file_name_1 != None and "RPS" in file_name_1:
            fileType = 'RPS'

        xl = pd.ExcelFile(inpfile)
        datatb = pd.DataFrame()       
        columns = None
        for idx, sheetName in enumerate(xl.sheet_names):
            #print(f'Reading sheet #{idx}: {name}')
            if sheetName not in listCompany:
                dbops.logger.error('The sheetname is not valid')
                raise Exception('The sheetname is not valid')
                exit()    
            sheet = xl.parse(sheetName)
            sheet = sheet.loc[:, ~sheet.columns.str.contains('Unnamed')]
           
            #datatb = datatb.append(sheet, ignore_index=True)
            datatb = sheet
            dbYear = datatb.Year.unique()
            yearSize=0
            yearSize = int(len(dbYear))

            dfSetttings = pd.DataFrame(gvar.sadrd_settings)
            #dfSetttings = pd.DataFrame(gvar.sadrd_settings, columns = ['settingName', 'settingValue']) 
            #columns = ['settingName', 'settingValue']
           


            #df = pd.DataFrame(gvar.sadrd_settings, columns=columns)

            #dfYear = df.loc[df['settingName'] == 'SADRD_Year']
            
            for (row, sadrdSysSetting) in enumerate(gvar.sadrd_settings):
                if sadrdSysSetting.settingName == "SADRD_Year":
                    sadrdYear = sadrdSysSetting.settingValue
                    break
                
            if yearSize == 1:                
                distYear = datatb.Year.unique()[0]
                if int(distYear) != int(year):
                    dbops.logger.error('The UI year does not match with year in file')
                    raise Exception('The UI year does not match with year in file')
                    exit()
                if int(year) != int(sadrdYear):
                    raise Exception('This in not a valid year with respect to year in sys_settings table')
                    exit()
            elif yearSize > 1: 
                dbops.logger.error('The file data contains more than one year')
                raise Exception('Contains more than one year')
                exit()

            dbCompany = datatb.Company.unique()
            companySize=0
            companySize = int(len(dbCompany))

            if companySize == 1:
                distCompany = datatb.Company.unique()[0]
                if distCompany not in listCompany:
                    raise Exception('This in not a valid company with respect to company in sys_settings table')
                    exit()
            elif companySize > 1: 
                dbops.logger.error('The file data contains more than one company')
                raise Exception('Contains more than one company')
                exit()
            
            
            loadkey_dict = dict(
                    [('companyName', distCompany), ('year', str(distYear)), ('fileType',fileType)])

            for index, (key, value) in enumerate(loadkey_dict.items()):
                loadkey = value if index == 0 else loadkey + ";" + value
            strJsonVal = json.dumps(loadkey_dict, indent=4)

            
            datatb = sheet.iloc[1:]
            datatb.columns =['Year', 'Company', 'Cusip', 'CusipName', 'Bank', 'FTC', 'QualPct']
            datatb = datatb
            datatb.insert(7, 'Source', fileType)
            datatb.insert(8, 'FileName', file_name)

            datatb['Cusip'] = datatb['Cusip'].astype(str)
            datatb['CusipName'] = datatb['CusipName'].astype(str)
            datatb['Bank'] = datatb['Bank'].astype(str)
            datatb['FTC'] = datatb['FTC'].fillna(0)
            datatb['QualPct'] = datatb['QualPct'].fillna(0)
            dbops_obj.insert_dataloadkey(loadkey, strJsonVal,  actionname, gvar.INPROGRESS, str(datetime.datetime.now().strftime('%Y-%m-%d'))[0:10], gvar.user_id)
            dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, actionname, 'parseCusipQualFTCFile', str(datetime.datetime.now())[0:23], "insert_dataloadkey method executed", gvar.dataload_id)
            
            dbops_obj.loaddata('SADRD_SrcStaging_CusipQualFTC', datatb, '')
            dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, actionname, 'parseCusipQualFTCFile', str(datetime.datetime.now())[0:23], "loaddata method executed for SADRD_SrcStaging_CusipQualFTC", gvar.dataload_id)
        
            dbops_obj.executeSADRD_SP('sp_SADRD_LoadCusipQualFTC')
            dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, actionname, 'parseCusipQualFTCFile', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_LoadCusipQualFTC", gvar.dataload_id)
            
            dbops_obj.executeSADRD_SP('sp_SADRD_UpdateCntrlTotals_CusipQualFTC')
            dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, actionname, 'parseCusipQualFTCFile', str(datetime.datetime.now())[0:23], "executeSADRD_SP method executed for SP sp_SADRD_UpdateCntrlTotals_CusipQualFTC", gvar.dataload_id)
        
    except Exception as e:
        dbops_obj.insert_actionLog(datetime.datetime.now().month, datetime.datetime.now().year, gvar.user_id, actionname, 'parseCusipQualFTCFile', str(datetime.datetime.now())[0:23], ("Exception occurred in parseCusipQualFTCFile - " + str(e)), None)
        dbops.logger.error('Error in parsefile -' + str(e))
        raise e
'''
