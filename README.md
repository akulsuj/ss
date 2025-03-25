import unittest
from unittest.mock import patch, MagicMock
import pandas as pd
import datetime
import os
import json

# Assuming SADRD_Dataparser.py and Services/dboperations.py are in the same directory or in your PYTHONPATH
import SADRD_Dataparser
from Services import dboperations

class TestSADRDDataparser(unittest.TestCase):

    def setUp(self):
        self.mock_dbops_obj = MagicMock(spec=dboperations.dboperations)
        self.patcher_dbops = patch('SADRD_Dataparser.dbops_obj', self.mock_dbops_obj)
        self.patcher_dbops.start()

        self.mock_gvar = MagicMock()
        self.patcher_gvar = patch('SADRD_Dataparser.gvar', self.mock_gvar)
        self.patcher_gvar.start()

        self.mock_datetime = MagicMock()
        self.patcher_datetime = patch('SADRD_Dataparser.datetime', self.mock_datetime)
        self.patcher_datetime.start()

        SADRD_Dataparser.dbops_obj = self.mock_dbops_obj
        SADRD_Dataparser.gvar = self.mock_gvar

        self.mock_gvar.sadrd_settings = [
            MagicMock(settingName='SADRD_Year', settingValue='2023'),
            MagicMock(settingName='Valid_Company', settingValue='CompanyA'),
            MagicMock(settingName='Valid_Company', settingValue='CompanyB'),
        ]
        SADRD_Dataparser.listCompany = ['CompanyA', 'CompanyB']
        SADRD_Dataparser.gvar.sadrdYear = 2023
        self.mock_datetime.datetime.now.return_value = datetime.datetime(2023, 1, 1, 12, 0, 0)
        self.mock_gvar.user_id = 'test_user'
        self.mock_gvar.INPROGRESS = 'InProgress'
        self.mock_gvar.COMPLETED = 'Completed'
        self.mock_gvar.filesLoadedCount = 0
        self.mock_gvar.fileErrorMessages = ''

    def tearDown(self):
        self.patcher_dbops.stop()
        self.patcher_gvar.stop()
        self.patcher_datetime.stop()

    def test_parseAnnualStmntFile_success(self):
        df = pd.DataFrame({'Cusip': ['123-45', '67890'], 'DividendAmt': [100, 200]})
        self.mock_dbops_obj.executeNIR_SP.return_value = df

        SADRD_Dataparser.parseAnnualStmntFile('CompanyA', 'Part 2 Section 1', 'test_action', 2023, "")

        self.mock_dbops_obj.insert_dataloadkey.assert_called()
        self.mock_dbops_obj.loaddata.assert_called()
        self.mock_dbops_obj.executeSADRD_SP.assert_called()
        self.assertEqual(SADRD_Dataparser.gvar.filesLoadedCount, 1)

    def test_parseAnnualStmntFile_secondary_validation(self):
        SADRD_Dataparser.parseAnnualStmntFile('CompanyA', 'Part 2 Section 1', 'test_action', 2023, "SecondaryValidation")
        self.mock_dbops_obj.executeNIR_SP.assert_not_called()

    def test_parseAnnualStmntFile_exception(self):
        self.mock_dbops_obj.executeNIR_SP.side_effect = Exception("Test Exception")

        with self.assertRaises(Exception):
            SADRD_Dataparser.parseAnnualStmntFile('CompanyA', 'Part 2 Section 1', 'test_action', 2023, "")

    def test_parseCusipQualFTCFile_secondary_validation_errors(self):
        mock_excel = MagicMock()
        mock_sheet1 = MagicMock()
        mock_sheet2 = MagicMock()
        mock_sheet1.title = 'CompanyC'
        mock_sheet2.title = 'CompanyA'
        mock_sheet2.sheet_state = 'visible'
        mock_sheet1.sheet_state = 'visible'
        mock_excel.book.worksheets = [mock_sheet1, mock_sheet2]
        mock_excel.parse.return_value = pd.DataFrame({'Year': [2024], 'Company':['CompanyA']})
        with patch('SADRD_Dataparser.pd.ExcelFile', return_value=mock_excel):
            SADRD_Dataparser.parseCusipQualFTCFile('test_action', 'test.xlsx', 2023, 'SecondaryValidation')
        self.assertTrue('E020' in SADRD_Dataparser.gvar.fileErrorMessages)
        self.assertTrue('E005' in SADRD_Dataparser.gvar.fileErrorMessages)

    def test_parseCusipQualFTCFile_success(self):
        mock_excel = MagicMock()
        mock_sheet = MagicMock()
        mock_sheet.title = 'CompanyA'
        mock_sheet.sheet_state = 'visible'
        mock_excel.book.worksheets = [mock_sheet]
        mock_excel.parse.return_value = pd.DataFrame({'Year': [2023], 'Company': ['CompanyA'], 'Cusip': ['123'], 'CusipName': ['Test'], 'Bank': ['BankA'], 'FTC': [10], 'QualPct': [50]})
        with patch('SADRD_Dataparser.pd.ExcelFile', return_value=mock_excel):
            SADRD_Dataparser.parseCusipQualFTCFile('test_action', 'test.xlsx', 2023, "")
        self.mock_dbops_obj.insert_dataloadkey.assert_called()
        self.mock_dbops_obj.loaddata.assert_called()
        self.assertEqual(SADRD_Dataparser.gvar.filesLoadedCount, 1)

    def test_parseJHFundsFTCGrossupFile_secondary_validation_errors(self):
        mock_excel = MagicMock()
        mock_sheet = MagicMock()
        mock_sheet.title = 'Sheet1'
        mock_sheet.sheet_state = 'visible'
        mock_excel.book.worksheets = [mock_sheet]
        mock_excel.parse.return_value = pd.DataFrame({'Year': [2024], 'Quarter': [2]})
        with patch('SADRD_Dataparser.pd.ExcelFile', return_value=mock_excel):
            SADRD_Dataparser.parseJHFundsFTCGrossupFile('test_action', '2023_Q1_JHFunds.xlsx', 2023, 'SecondaryValidation')
        self.assertTrue('E005' in SADRD_Dataparser.gvar.fileErrorMessages)
        self.assertTrue('E010' in SADRD_Dataparser.gvar.fileErrorMessages)

    def test_parseJHFundsFTCGrossupFile_success(self):
        mock_excel = MagicMock()
        mock_sheet = MagicMock()
        mock_sheet.title = 'Sheet1'
        mock_sheet.sheet_state = 'visible'
        mock_excel.book.worksheets = [mock_sheet]
        mock_excel.parse.return_value = pd.DataFrame({'Year': [2023], 'Quarter': [1], 'Bank': ['BankA'], 'Trust': ['TrustA'], 'JHNY%Share': [10], 'JHUSA%Share': [20], 'Other%Share': [30], 'Total%Share': [60], 'Fund Name':['FundA'], 'JHNY-Div':[100], 'JHUSA-Div':[200], 'Other-Div':[300], 'Total All-Div':[600]})
        with patch('SADRD_Dataparser.pd.ExcelFile', return_value=mock_excel):
            SADRD_Dataparser.parseJHFundsFTCGrossupFile('test_action', '2023_Q1_JHFunds.xlsx', 2023, "")
        self.mock_dbops_obj.insert_dataloadkey.assert_called()
        self.mock_dbops_
