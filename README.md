import unittest
from unittest.mock import MagicMock, patch, call
import pandas as pd
import datetime
import os
import json
from SADRD_Dataparser import (
    parseAnnualStmntFile,
    parseCusipQualFTCFile,
    parseJHFundsFTCGrossupFile,
    executeView_GetDetails,
    Load_FundCusipDetails,
    LoadVPADataToSADRD
)
import globalvars as gvar
from Services import dboperations as dbops

class TestSADRDDataparser(unittest.TestCase):
    
    def setUp(self):
        # Setup global variables
        gvar.sadrd_settings = [MagicMock(settingName='SADRD_Year', settingValue='2023'),
                              MagicMock(settingName='Valid_Company', settingValue='TestCompany')]
        gvar.sadrdYear = 2023
        gvar.user_id = 'test_user'
        gvar.dataload_id = 1
        gvar.filesLoadedCount = 0
        gvar.fileErrorMessages = ''
        gvar.INPROGRESS = 'INPROGRESS'
        gvar.COMPLETED = 'COMPLETED'
        
        # Create mock for dboperations
        self.mock_dbops = MagicMock(spec=dbops.dboperations())
        
        # Patch the dbops_obj in SADRD_Dataparser
        self.patcher = patch('SADRD_Dataparser.dbops_obj', self.mock_dbops)
        self.patcher.start()
        
    def tearDown(self):
        self.patcher.stop()
        
    def test_parseAnnualStmntFile_success_part2_section1(self):
        # Test data
        company = "TestCompany"
        partType = "Part 2 Section 1"
        actionname = "test_action"
        AnnualStmntYear = 2023
        SecondaryValidation = ""
        
        # Mock database response
        test_data = pd.DataFrame({
            'Cusip': ['123456789', '987654321'],
            'DividendAmt': [100.0, 200.0]
        })
        self.mock_dbops.executeNIR_SP.return_value = test_data
        
        # Execute function
        parseAnnualStmntFile(company, partType, actionname, AnnualStmntYear, SecondaryValidation)
        
        # Verify calls
        expected_sp = "[sadrd].[uspGenerateDP2S1FeedReportToSADRD]"
        self.mock_dbops.executeNIR_SP.assert_called_once_with(expected_sp, 2023, "TestCompany")
        
        # Verify loadkey was inserted
        self.mock_dbops.insert_dataloadkey.assert_called_once()
        
        # Verify data was loaded
        self.mock_dbops.loaddata.assert_called()
        
        # Verify files loaded count was incremented
        self.assertEqual(gvar.filesLoadedCount, 1)
        
    def test_parseAnnualStmntFile_success_part4(self):
        # Test data
        company = "TestCompany"
        partType = "Part 4"
        actionname = "test_action"
        AnnualStmntYear = 2023
        SecondaryValidation = ""
        
        # Mock database response
        test_data = pd.DataFrame({
            'Cusip': ['123456789', '987654321'],
            'DividendAmt': [100.0, 200.0]
        })
        self.mock_dbops.executeNIR_SP.return_value = test_data
        
        # Execute function
        parseAnnualStmntFile(company, partType, actionname, AnnualStmntYear, SecondaryValidation)
        
        # Verify calls
        expected_sp = "[sadrd].[uspGenerateDP4FeedReportToSADRD]"
        self.mock_dbops.executeNIR_SP.assert_called_once_with(expected_sp, 2023, "TestCompany")
        
    def test_parseAnnualStmntFile_secondary_validation(self):
        # Test data
        company = "TestCompany"
        partType = "Part 2 Section 1"
        actionname = "test_action"
        AnnualStmntYear = 2023
        SecondaryValidation = "Y"
        
        # Execute function
        parseAnnualStmntFile(company, partType, actionname, AnnualStmntYear, SecondaryValidation)
        
        # Verify no database calls were made
        self.mock_dbops.executeNIR_SP.assert_not_called()
        self.mock_dbops.insert_dataloadkey.assert_not_called()
        
    def test_parseCusipQualFTCFile_success(self):
        # Test data
        actionname = "test_action"
        inpfile = "JHFunds_test.xlsx"
        UIYear = 2023
        SecondaryValidation = ""
        
        # Create a temporary test file
        with open(inpfile, 'w') as f:
            f.write("Test content")
            
        # Mock Excel file parsing
        mock_sheet = MagicMock()
        mock_sheet.title = "TestCompany"
        mock_sheet.sheet_state = "visible"
        
        mock_excel = MagicMock()
        mock_excel.book.worksheets = [mock_sheet]
        mock_excel.parse.return_value = pd.DataFrame({
            'Year': [2023],
            'Company': ['TestCompany'],
            'Cusip': ['123456789'],
            'CusipName': ['Test Cusip'],
            'Bank': ['Test Bank'],
            'FTC': [0.5],
            'QualPct': [0.8]
        })
        
        with patch('pandas.ExcelFile', return_value=mock_excel):
            parseCusipQualFTCFile(actionname, inpfile, UIYear, SecondaryValidation)
        
        # Verify calls
        self.mock_dbops.insert_dataloadkey.assert_called_once()
        self.mock_dbops.loaddata.assert_called_once()
        
        # Clean up
        os.remove(inpfile)
        
    def test_parseCusipQualFTCFile_invalid_company(self):
        # Test data
        actionname = "test_action"
        inpfile = "JHFunds_test.xlsx"
        UIYear = 2023
        SecondaryValidation = ""
        
        # Create a temporary test file
        with open(inpfile, 'w') as f:
            f.write("Test content")
            
        # Mock Excel file parsing with invalid company
        mock_sheet = MagicMock()
        mock_sheet.title = "InvalidCompany"
        mock_sheet.sheet_state = "visible"
        
        mock_excel = MagicMock()
        mock_excel.book.worksheets = [mock_sheet]
        
        with patch('pandas.ExcelFile', return_value=mock_excel):
            parseCusipQualFTCFile(actionname, inpfile, UIYear, SecondaryValidation)
        
        # Verify error message was set
        self.assertIn("E020", gvar.fileErrorMessages)
        
        # Clean up
        os.remove(inpfile)
        
    def test_parseJHFundsFTCGrossupFile_success(self):
        # Test data
        actionname = "test_action"
        inpfile = "2023_Q1_JHFunds.xlsx"
        FTCGrossupYear = 2023
        SecondaryValidation = ""
        
        # Create a temporary test file
        with open(inpfile, 'w') as f:
            f.write("Test content")
            
        # Mock Excel file parsing
        mock_sheet = MagicMock()
        mock_sheet.title = "Sheet1"
        mock_sheet.sheet_state = "visible"
        
        mock_excel = MagicMock()
        mock_excel.book.worksheets = [mock_sheet]
        mock_excel.parse.return_value = pd.DataFrame({
            'Year': [2023],
            'Quarter': [1],
            'Bank': ['Test Bank'],
            'Trust': ['Test Trust'],
            'JHNY%Share': [0.3],
            'JHUSA%Share': [0.4],
            'Other%Share': [0.3],
            'Total%Share': [1.0],
            'Fund Name': ['Test Fund'],
            'JHNY-Div': [100.0],
            'JHUSA-Div': [150.0],
            'Other-Div': [50.0],
            'Total All-Div': [300.0]
        })
        
        with patch('pandas.ExcelFile', return_value=mock_excel):
            parseJHFundsFTCGrossupFile(actionname, inpfile, FTCGrossupYear, SecondaryValidation)
        
        # Verify calls
        self.mock_dbops.insert_dataloadkey.assert_called_once()
        self.mock_dbops.loaddata.assert_called_once()
        
        # Clean up
        os.remove(inpfile)
        
    def test_executeView_GetDetails_success(self):
        # Test data
        importType = "test_import"
        cusipType = "test_cusip"
        
        # Mock database response
        expected_result = pd.DataFrame({'col1': [1, 2], 'col2': ['a', 'b']})
        self.mock_dbops.executeView_GetDetails.return_value = expected_result
        
        # Execute function
        result = executeView_GetDetails(importType, cusipType)
        
        # Verify
        self.mock_dbops.executeView_GetDetails.assert_called_once_with(importType, cusipType)
        self.assertTrue(result.equals(expected_result))
        
    def test_Load_FundCusipDetails_success(self):
        # Test data
        actionname = "test_action"
        dfCusipDetails = pd.DataFrame({
            'Cusip': ['123456789'],
            'FundName': ['Test Fund']
        })
        fundCusipYear = 2023
        
        # Execute function
        Load_FundCusipDetails(actionname, dfCusipDetails, fundCusipYear)
        
        # Verify calls
        self.mock_dbops.insert_dataloadkey.assert_called_once()
        self.mock_dbops.loaddata.assert_called_once()
        
    def test_LoadVPADataToSADRD_GL_success(self):
        # Test data
        actionName = "test_action"
        fundCusipYear = 2023
        dataType = "GL"
        
        # Mock database responses
        mock_vpa_data = pd.DataFrame({
            'Year': [2023],
            'Company': ['TestCompany'],
            'Amount': [1000.0]
        })
        self.mock_dbops.GetVPAData.return_value = mock_vpa_data
        
        # Execute function
        LoadVPADataToSADRD(actionName, fundCusipYear, dataType)
        
        # Verify calls
        self.mock_dbops.GetVPAData.assert_called()
        self.mock_dbops.truncatetable.assert_called_once_with('SADRD_FactsTemp_CntrlTotals_Input', "GL")
        self.mock_dbops.InsertControlTotals.assert_called_once()
        self.mock_dbops.loaddata.assert_called_once()
        
    def test_LoadVPADataToSADRD_SeparateAccounts_success(self):
        # Test data
        actionName = "test_action"
        fundCusipYear = 2023
        dataType = "SeparateAccounts"
        
        # Mock database responses
        mock_vpa_data = pd.DataFrame({
            'Year': [2023],
            'Company': ['TestCompany'],
            'Account': ['TestAccount']
        })
        self.mock_dbops.GetVPAData.return_value = mock_vpa_data
        
        # Execute function
        LoadVPADataToSADRD(actionName, fundCusipYear, dataType)
        
        # Verify calls
        self.mock_dbops.GetVPAData.assert_called()
        self.mock_dbops.loaddata.assert_called_once()

if __name__ == '__main__':
    unittest.main()