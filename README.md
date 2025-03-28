import unittest
from unittest.mock import patch, Mock, MagicMock
import pandas as pd
import datetime
import json
import os
import logging
import sys

# Add the parent directory to sys.path
current_dir = os.path.dirname(os.path.abspath(__file__))
parent_dir = os.path.dirname(current_dir)
sys.path.append(parent_dir)

import SADRD_Dataparser

class TestSADRDDataparser(unittest.TestCase):

    def setUp(self):
        # Mocking dbops_obj and gvar for each test
        self.mock_dbops_obj = Mock()
        self.mock_gvar = Mock()
        SADRD_Dataparser.dbops_obj = self.mock_dbops_obj
        SADRD_Dataparser.gvar = self.mock_gvar

        # Mocking global variables
        self.mock_gvar.sadrd_settings = [Mock(settingName="SADRD_Year", settingValue="2023"),
                                         Mock(settingName="Valid_Company", settingValue="CompanyA"),
                                         Mock(settingName="Valid_Company", settingValue="CompanyB")]
        self.mock_gvar.sadrdYear = 2023
        self.mock_gvar.user_id = "test_user"
        self.mock_gvar.dataload_id = 123
        self.mock_gvar.INPROGRESS = "INPROGRESS"
        self.mock_gvar.COMPLETED = "COMPLETED"
        self.mock_gvar.filesLoadedCount = 0
        self.mock_gvar.fileErrorMessages = ""

        # Mocking datetime
        self.mock_datetime = Mock()
        self.mock_datetime.datetime.now.return_value = datetime.datetime(2023, 10, 26, 12, 0, 0)
        SADRD_Dataparser.datetime = self.mock_datetime
        SADRD_Dataparser.logging = Mock()

    def test_parseAnnualStmntFile_success(self):
        # Mocking dbops_obj.executeNIR_SP
        mock_data = {'Cusip': ['123-45', '678-90'], 'DividendAmt': [100, 200]}
        mock_df = pd.DataFrame(mock_data)
        self.mock_dbops_obj.executeNIR_SP.return_value = mock_df

        SADRD_Dataparser.parseAnnualStmntFile("CompanyA", "Part 2 Section 1", "test_action", 2023, "")

        self.mock_dbops_obj.executeNIR_SP.assert_called_once()
        self.mock_dbops_obj.insert_dataloadkey.assert_called()
        self.mock_dbops_obj.loaddata.assert_called()
        self.mock_dbops_obj.executeSADRD_SP.assert_called()
        self.assertEqual(self.mock_gvar.filesLoadedCount, 1)

    def test_parseAnnualStmntFile_secondary_validation(self):
        SADRD_Dataparser.parseAnnualStmntFile("CompanyA", "Part 2 Section 1", "test_action", 2023, "SecondaryValidation")
        self.mock_dbops_obj.executeNIR_SP.assert_not_called()

    def test_parseAnnualStmntFile_exception(self):
        self.mock_dbops_obj.executeNIR_SP.side_effect = Exception("Test Exception")
        with self.assertRaises(Exception):
            SADRD_Dataparser.parseAnnualStmntFile("CompanyA", "Part 2 Section 1", "test_action", 2023, "")

    def test_parseCusipQualFTCFile_secondary_validation_errors(self):
        mock_excel = Mock()
        mock_sheet1 = Mock()
        mock_sheet1.title = "InvalidCompany"
        mock_sheet1.sheet_state = "visible"
        mock_excel.book.worksheets = [mock_sheet1]
        with patch('pandas.ExcelFile', return_value=mock_excel):
            SADRD_Dataparser.parseCusipQualFTCFile("test_action", "test.xlsx", 2023, "SecondaryValidation")
        self.assertTrue("E020" in self.mock_gvar.fileErrorMessages)

    def test_parseCusipQualFTCFile_secondary_validation_year_error(self):
        mock_excel = Mock()
        mock_sheet1 = Mock()
        mock_sheet1.title = "CompanyA"
        mock_sheet1.sheet_state = "visible"
        mock_df = pd.DataFrame({'Year': [2022], 'Company': ['CompanyA']})
        mock_excel.parse.return_value = mock_df
        mock_excel.book.worksheets = [mock_sheet1]

        with patch('pandas.ExcelFile', return_value=mock_excel):
            SADRD_Dataparser.parseCusipQualFTCFile("test_action", "test.xlsx", 2023, "SecondaryValidation")
        self.assertTrue("E005" in self.mock_gvar.fileErrorMessages)

    def test_parseCusipQualFTCFile_success(self):
        mock_excel = Mock()
        mock_sheet1 = Mock()
        mock_sheet1.title = "CompanyA"
        mock_sheet1.sheet_state = "visible"
        mock_df = pd.DataFrame({'Year': [2023], 'Company': ['CompanyA'], 'Cusip': ['123'], 'CusipName': ['TestCusip'], 'Bank': ['TestBank'], 'FTC': [100], 'QualPct': [50]})
        mock_excel.parse.return_value = mock_df
        mock_excel.book.worksheets = [mock_sheet1]

        with patch('pandas.ExcelFile', return_value=mock_excel):
            SADRD_Dataparser.parseCusipQualFTCFile("test_action", "test.xlsx", 2023, "")

        self.mock_dbops_obj.insert_dataloadkey.assert_called()
        self.mock_dbops_obj.loaddata.assert_called()
        self.assertEqual(self.mock_gvar.filesLoadedCount, 1)

    def test_parseCusipQualFTCFile_exception(self):
        with patch('pandas.ExcelFile', side_effect=Exception("Test Exception")):
            with self.assertRaises(Exception):
                SADRD_Dataparser.parseCusipQualFTCFile("test_action", "test.xlsx", 2023, "")

    def test_parseJHFundsFTCGrossupFile_secondary_validation_year_error(self):
        mock_excel = Mock()
        mock_sheet1 = Mock()
        mock_sheet1.title = "Sheet1"
        mock_sheet1.sheet_state = "visible"
        mock_df = pd.DataFrame({'Year': [2022], 'Quarter': [3]})
        mock_excel.parse.return_value = mock_df
        mock_excel.book.worksheets = [mock_sheet1]

        with patch('pandas.ExcelFile', return_value=mock_excel):
            SADRD_Dataparser.parseJHFundsFTCGrossupFile("test_action", "2023_Q3_JHFunds.xlsx", 2023, "SecondaryValidation")
        self.assertTrue("E005" in self.mock_gvar.fileErrorMessages)

    def test_parseJHFundsFTCGrossupFile_success(self):
        mock_excel = Mock()
        mock_sheet1 = Mock()
        mock_sheet1.title = "Sheet1"
        mock_sheet1.sheet_state = "visible"
        mock_df = pd.DataFrame({'Year': [2023], 'Quarter': [3], 'Bank': ['TestBank'], 'Trust': ['TestTrust'], 'JHNY%Share': [10], 'JHUSA%Share': [20], 'Other%Share': [30], 'Total%Share': [60], 'Fund Name': ['TestFund'], 'JHNY-Div': [100], 'JHUSA-Div': [200], 'Other-Div': [300], 'Total All-Div': [600]})
        mock_excel.parse.return_value = mock_df
