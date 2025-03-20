import unittest
from unittest.mock import MagicMock, patch
import os
import datetime
import shutil

mock_method = patch('Services.dboperations', spec=True).start()
with patch("Services.dboperations") as mock_dbos:
    import Services.fileoperations as fo
    from Services.CustomException import FileValidationException
    from Entities.Customentities import ApihomeResp
    import globalvars as gvar

class Test_FileOperations(unittest.TestCase):

    def setUp(self):
        self.mock_dbops_obj = MagicMock()
        fo.dbops_obj = self.mock_dbops_obj
        gvar.sadrd_settings = [
            MagicMock(settingName="Valid_Company", settingValue="JHUSA"),
            MagicMock(settingName="Valid_Quarter", settingValue="Q1"),
            MagicMock(settingName="Valid_Quarter", settingValue="Q2"),
            MagicMock(settingName="Valid_Quarter", settingValue="Q3"),
            MagicMock(settingName="Valid_Quarter", settingValue="Q4"),
            MagicMock(settingName="Filename_AnnStmtSchD", settingValue="SchD"),
            MagicMock(settingName="Filename_QualFTC", settingValue="QualFTC"),
            MagicMock(settingName="Filename_FTCGrossup", settingValue="FTCGrossup"),
        ]
        gvar.sadrd_ErrMessages = MagicMock()
        gvar.user_id = 1

    @patch('os.path.join')
    @patch('os.listdir')
    def test_getinpfilenames_toprocess(self, mock_listdir, mock_path_join):
        folderpath = 'test_folder'
        inpLoadFolder = 'input_files'

        mock_path_join.side_effect = lambda a, b: os.path.join(a, b)
        mock_listdir.return_value = [
            'file1.xlsx', 'file2.xlsm', 'file3.csv', 'file4.txt'
        ]

        expected_files = [
            os.path.join('test_folder', 'input_files', 'file1.xlsx'),
            os.path.join('test_folder', 'input_files', 'file2.xlsm'),
            os.path.join('test_folder', 'input_files', 'file3.csv')
        ]

        result = fo.getinpfilenames_toprocess(folderpath, inpLoadFolder)

        self.assertEqual(result, expected_files)

    @patch('os.path.exists')
    @patch('os.makedirs')
    @patch('os.listdir')
    @patch('shutil.copyfile')
    @patch('Services.fileoperations.is_open')
    def test_Downloadfilenames_toprocess_annual_schd_success(self, mock_is_open, mock_copyfile, mock_listdir, mock_makedirs, mock_exists):
        serverInputFilesByAction = 'server_files'
        Inputdirpath = 'local_files'
        action = 'Annual Stmt - Sch D'
        year = '2023'
        mock_exists.return_value = True
        mock_listdir.return_value = ['2023JHUSASchD.csv']
        mock_is_open.return_value = False

        result = fo.Downloadfilenames_toprocess(serverInputFilesByAction, Inputdirpath, action, year)
        self.assertEqual(result, [os.path.join('local_files', '2023JHUSASchD.csv')])

    @patch('os.path.exists')
    @patch('os.makedirs')
    @patch('os.listdir')
    @patch('shutil.copyfile')
    @patch('Services.fileoperations.is_open')
    def test_Downloadfilenames_toprocess_qualftc_success(self, mock_is_open, mock_copyfile, mock_listdir, mock_makedirs, mock_exists):
        serverInputFilesByAction = 'server_files'
        Inputdirpath = 'local_files'
        action = 'QualPctFTC'
        year = '2023'
        mock_exists.return_value = True
        mock_listdir.return_value = ['2023QualFTC.xlsx']
        mock_is_open.return_value = False

        result = fo.Downloadfilenames_toprocess(serverInputFilesByAction, Inputdirpath, action, year)
        self.assertEqual(result, [os.path.join('local_files', '2023QualFTC.xlsx')])

    @patch('os.path.exists')
    @patch('os.makedirs')
    @patch('os.listdir')
    @patch('shutil.copyfile')
    @patch('Services.fileoperations.is_open')
    def test_Downloadfilenames_toprocess_ftcgrossup_success(self, mock_is_open, mock_copyfile, mock_listdir, mock_makedirs, mock_exists):
        serverInputFilesByAction = 'server_files'
        Inputdirpath = 'local_files'
        action = 'FTCGrossup'
        year = '2023'
        mock_exists.return_value = True
        mock_listdir.return_value = ['2023_Q1_FTCGrossup.xlsx', '2023_Q2_FTCGrossup.xlsx', '2023_Q3_FTCGrossup.xlsx', '2023_Q4_FTCGrossup.xlsx']
        mock_is_open.return_value = False

        expected = [os.path.join('local_files', f'2023_Q{i}_FTCGrossup.xlsx') for i in range(1, 5)]
        result = fo.Downloadfilenames_toprocess(serverInputFilesByAction, Inputdirpath, action, year)
        self.assertEqual(result, expected)

    @patch('os.path.exists')
    @patch('os.makedirs')
    @patch('os.listdir')
    @patch('shutil.copyfile')
    @patch('Services.fileoperations.is_open')
    def test_Downloadfilenames_toprocess_wrong_file_type(self, mock_is_open, mock_copyfile, mock_listdir, mock_makedirs, mock_exists):
        serverInputFilesByAction = 'server_files'
        Inputdirpath = 'local_files'
        action = 'Annual Stmt - Sch D'
        year = '2023'
        mock_exists.return_value = True
        mock_listdir.return_value = ['2023JHUSASchD.xlsx']
        mock_is_open.return_value = False
        self.mock_dbops_obj.BuildErrorMessage.return_value = "Error"
        with self.assertRaises(FileValidationException):
            fo.Downloadfilenames_toprocess(serverInputFilesByAction, Inputdirpath, action, year)
