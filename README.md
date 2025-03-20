pytest --cov . test/ --cov-report html
===================================================== test session starts =====================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 83 items

test\test_APIHome.py .........                                                                                           [ 10%]
test\test_config.py .....................                                                                                [ 36%]
test\test_db.py .                                                                                                        [ 37%] 
test\test_globalvars.py .                                                                                                [ 38%] 
test\Entities\test_Customentities.py ....                                                                                [ 43%]
test\Entities\test_dbormschemas.py .........                                                                             [ 54%]
test\Services\test_APIResponse.py .......                                                                                [ 62%]
test\Services\test_Auth.py ........                                                                                      [ 72%]
test\Services\test_CustomException.py ..                                                                                 [ 74%]
test\Services\test_dboperations.py ...........                                                                           [ 87%]
test\Services\test_fileoperations.py ....                                                                                [ 92%]
test\Services\test_logoperations.py ...                                                                                  [ 96%]
test\Services\test_parentparser.py FFF                                                                                   [100%]

========================================================== FAILURES =========================================================== 
_______________________________________ TestParentParser.test_parentparser_annual_stmt ________________________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_annual_stmt>
mock_parse_annual = <MagicMock name='parseAnnualStmntFile' id='2077781231744'>
mock_download = <MagicMock name='DownloadServerFilesToLoad' id='2077781240368'>
mock_dbops_constructor = <MagicMock name='dboperations' id='2077780083760'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    @patch('Services.parentparser.sdp.parseAnnualStmntFile')
    def test_parentparser_annual_stmt(self, mock_parse_annual, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [
            MagicMock(settingName="Valid_Company", settingValue="test_company"),
            MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")
        ]
        parentparser({"action1": ["file1.txt"]}, "/input/dir/", "Annual Stmt - Sch D", 2025)
>       mock_parse_annual.assert_called()

test\Services\test_parentparser.py:62:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='parseAnnualStmntFile' id='2077781231744'>

    def assert_called(self):
        """assert that the mock was called at least once
        """
        if self.call_count == 0:
            msg = ("Expected '%s' to have been called." %
                   (self._mock_name or 'mock'))
>           raise AssertionError(msg)
E           AssertionError: Expected 'parseAnnualStmntFile' to have been called.

C:\Program Files\Python39\lib\unittest\mock.py:876: AssertionError
__________________________________ TestParentParser.test_parentparser_generate_sadrd_report ___________________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_generate_sadrd_report>
mock_load_vpa = <MagicMock name='LoadVPADataToSADRD' id='2077780121248'>
mock_load_cusip = <MagicMock name='Load_FundCusipDetails' id='2077779182160'>
mock_execute_view = <MagicMock name='executeView_GetDetails' id='2077781309136'>
mock_dbops_constructor = <MagicMock name='dboperations' id='2077781141872'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.sdp.executeView_GetDetails')
    @patch('Services.parentparser.sdp.Load_FundCusipDetails')
    @patch('Services.parentparser.sdp.LoadVPADataToSADRD')
    def test_parentparser_generate_sadrd_report(self, mock_load_vpa, mock_load_cusip, mock_execute_view, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [
            MagicMock(settingName="IsRefreshUVAndVPA", settingValue="Y"),
            MagicMock(settingName="Valid_Company", settingValue="test_company"),
            MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")
        ]
        mock_execute_view.return_value = pd.DataFrame()
>       parentparser({"action1": ["file1.txt"]}, "/input/dir/", "generateSADRDReport", 2025)

test\Services\test_parentparser.py:78:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
Services\parentparser.py:224: in parentparser
    raise e
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

serverInputFilesByAction = {'action1': ['file1.txt']}, Inputdirpath = '/input/dir/', importType = 'generateSADRDReport'
Year = 2025, copyFlag = 'No'

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
>               isRefreshUVAndVPA = [x for x in gvar.sadrd_settings if x.settingName == 'IsRefreshUVAndVPA'][0].settingValue    
E               IndexError: list index out of range

Services\parentparser.py:154: IndexError
---------------------------------------------------- Captured stderr call ----------------------------------------------------- 
root        : ERROR    Exception occurred in parentparser() method :: list index out of range
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\parentparser.py", line 154, in parentparser
    isRefreshUVAndVPA = [x for x in gvar.sadrd_settings if x.settingName == 'IsRefreshUVAndVPA'][0].settingValue
IndexError: list index out of range
------------------------------------------------------ Captured log call ------------------------------------------------------ 
ERROR    root:parentparser.py:220 Exception occurred in parentparser() method :: list index out of range
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\parentparser.py", line 154, in parentparser
    isRefreshUVAndVPA = [x for x in gvar.sadrd_settings if x.settingName == 'IsRefreshUVAndVPA'][0].settingValue
IndexError: list index out of range
_____________________________ TestParentParser.test_parentparser_generate_sadrd_report_no_refresh _____________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_generate_sadrd_report_no_refresh>
mock_execute_view = <MagicMock name='executeView_GetDetails' id='2077782161584'>
mock_dbops_constructor = <MagicMock name='dboperations' id='2077781199024'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.sdp.executeView_GetDetails')
    def test_parentparser_generate_sadrd_report_no_refresh(self, mock_execute_view, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [
            MagicMock(settingName="IsRefreshUVAndVPA", settingValue="N"),
            MagicMock(settingName="Valid_Company", settingValue="test_company"),
            MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")
        ]
>       parentparser({"action1": ["file1.txt"]}, "/input/dir/", "generateSADRDReport", 2025)

test\Services\test_parentparser.py:94:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
Services\parentparser.py:224: in parentparser
    raise e
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

serverInputFilesByAction = {'action1': ['file1.txt']}, Inputdirpath = '/input/dir/', importType = 'generateSADRDReport'
Year = 2025, copyFlag = 'No'

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
>               isRefreshUVAndVPA = [x for x in gvar.sadrd_settings if x.settingName == 'IsRefreshUVAndVPA'][0].settingValue    
E               IndexError: list index out of range

Services\parentparser.py:154: IndexError
---------------------------------------------------- Captured stderr call ----------------------------------------------------- 
root        : ERROR    Exception occurred in parentparser() method :: list index out of range
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\parentparser.py", line 154, in parentparser
    isRefreshUVAndVPA = [x for x in gvar.sadrd_settings if x.settingName == 'IsRefreshUVAndVPA'][0].settingValue
IndexError: list index out of range
------------------------------------------------------ Captured log call ------------------------------------------------------ 
ERROR    root:parentparser.py:220 Exception occurred in parentparser() method :: list index out of range
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\parentparser.py", line 154, in parentparser
    isRefreshUVAndVPA = [x for x in gvar.sadrd_settings if x.settingName == 'IsRefreshUVAndVPA'][0].settingValue
IndexError: list index out of range
====================================================== warnings summary ======================================================= 
venv\lib\site-packages\pandas\compat\numpy\__init__.py:10
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:10: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    _nlv = LooseVersion(_np_version)

venv\lib\site-packages\pandas\compat\numpy\__init__.py:11
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:11: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    np_version_under1p17 = _nlv < LooseVersion("1.17")

venv\lib\site-packages\pandas\compat\numpy\__init__.py:12
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:12: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    np_version_under1p18 = _nlv < LooseVersion("1.18")

venv\lib\site-packages\pandas\compat\numpy\__init__.py:13
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:13: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    _np_version_under1p19 = _nlv < LooseVersion("1.19")

venv\lib\site-packages\pandas\compat\numpy\__init__.py:14
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:14: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    _np_version_under1p20 = _nlv < LooseVersion("1.20")

venv\lib\site-packages\setuptools\_distutils\version.py:337
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\setuptools\_distutils\version.py:337: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    other = LooseVersion(other)

venv\lib\site-packages\pandas\compat\numpy\function.py:120
venv\lib\site-packages\pandas\compat\numpy\function.py:120
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\function.py:120: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    if LooseVersion(__version__) >= LooseVersion("1.17.0"):

venv\lib\site-packages\flask_sqlalchemy\__init__.py:14
venv\lib\site-packages\flask_sqlalchemy\__init__.py:14
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\flask_sqlalchemy\__init__.py:14: DeprecationWarning: '_app_ctx_stack' is deprecated and will be removed in Flask 2.3.
    from flask import _app_ctx_stack, abort, current_app, request

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html

---------- coverage: platform win32, python 3.9.13-final-0 -----------
Coverage HTML written to dir htmlcov

=================================================== short test summary info =================================================== 
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_annual_stmt - AssertionError: Expected 'parseAnnualStmntFile' to have been called.
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_generate_sadrd_report - IndexError: list index out of range
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_generate_sadrd_report_no_refresh - IndexError: list index out of range
========================================== 3 failed, 80 passed, 10 warnings in 5.04s ========================================== 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
