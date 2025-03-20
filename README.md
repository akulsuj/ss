import unittest
from unittest.mock import MagicMock, patch, mock_open
import sys
import os
import datetime
import shutil
import pandas as pd
from Services import CustomException

sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '..', '..', 'Services')))

mock_sdp = MagicMock(name='SADRD_Dataparser')
mock_fiops = MagicMock(name='fileoperations')
mock_dbops = MagicMock(name='dboperations')
mock_globalvars = MagicMock(name='globalvars')

sys.modules['Services.SADRD_Dataparser'] = mock_sdp
sys.modules['Services.fileoperations'] = mock_fiops
sys.modules['Services.dboperations'] = mock_dbops
sys.modules['globalvars'] = mock_globalvars
sys.modules['Services.CustomException'] = CustomException

class TestParentParser(unittest.TestCase):

    def setUp(self):
        mock_globalvars.fileErrorMessages = ""
        mock_globalvars.filesLoadedCount = 0
        mock_globalvars.user_id = 1
        mock_globalvars.dataload_id = 1
        mock_globalvars.gconfig = {
            "DRIVER": "test_driver",
            "SADRD_DATABASE_SERVER": "test_sadr_server",
            "SADRD_DATABASE_NAME": "test_sadr_db",
            "CONNECTION_AUTH_STRING": "test_auth",
            "UV_DATABASE_SERVER": "test_uv_server",
            "UV_DATABASE_NAME": "test_uv_db",
            "UV_DATABASE_AUTH_STRING": "test_uv_auth",
            "VPA_DATABASE_SERVER": "test_vpa_server",
            "VPA_DATABASE_NAME": "test_vpa_db",
            "VPA_DATABASE_AUTH_STRING": "test_vpa_auth"
        }

    # ... (setUp and copyFileToOutputFolder tests from previous response) ...

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_qualpctftc(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='N')]
        mock_globalvars.sadrd_ErrMessages = []
        mock_globalvars.filesLoadedCount = 1
        mock_dbops_instance.SadrdSysSettings.return_value = mock_globalvars.sadrd_settings
        mock_dbops_instance.SADRD_Sys_Message.return_value = mock_globalvars.sadrd_ErrMessages

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "QualPctFTC"
        Year = 2025
        mock_download.return_value = ["/path/to/inputdir/file1.txt"]
        mock_sdp.parseCusipQualFTCFile.return_value = None
        mock_dbops_instance.BuildErrorMessage.return_value = "Success Message"
        mock_dbops_instance.executeSADRD_SP.return_value = None
        mock_dbops_instance.insert_actionLog.return_value = None

        result = parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)

        self.assertEqual(result.status, "Success")
        self.assertEqual(result.message, "QualPctFTC - Success Message")
        mock_sdp.parseCusipQualFTCFile.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_ftcgrossup(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='N')]
        mock_globalvars.sadrd_ErrMessages = []
        mock_globalvars.filesLoadedCount = 1
        mock_dbops_instance.SadrdSysSettings.return_value = mock_globalvars.sadrd_settings
        mock_dbops_instance.SADRD_Sys_Message.return_value = mock_globalvars.sadrd_ErrMessages

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "FTCGrossup"
        Year = 2025
        mock_download.return_value = ["/path/to/inputdir/file1.txt"]
        mock_sdp.parseJHFundsFTCGrossupFile.return_value = None
        mock_dbops_instance.BuildErrorMessage.return_value = "Success Message"
        mock_dbops_instance.executeSADRD_SP.return_value = None
        mock_dbops_instance.insert_actionLog.return_value = None

        result = parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)

        self.assertEqual(result.status, "Success")
        self.assertEqual(result.message, "FTCGrossup - Success Message")
        mock_sdp.parseJHFundsFTCGrossupFile.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_qualpctftc_file_error(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='N')]
        mock_globalvars.sadrd_ErrMessages = []
        mock_globalvars.fileErrorMessages = "Error Message"
        mock_dbops_instance.SadrdSysSettings.return_value = mock_globalvars.sadrd_settings
        mock_dbops_instance.SADRD_Sys_Message.return_value = mock_globalvars.sadrd_ErrMessages
        mock_dbops_instance.BuildErrorMessage.return_value = "Error Message"

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "QualPctFTC"
        Year = 2025
        mock_download.return_value = ["/path/to/inputdir/file1.txt"]
        mock_sdp.parseCusipQualFTCFile.return_value = None

        with self.assertRaises(CustomException.FileValidationException):
            parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_ftcgrossup_file_error_e026(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='N')]
        mock_globalvars.sadrd_ErrMessages = []
        mock_globalvars.fileErrorMessages = "E026, Error Message"
        mock_dbops_instance.SadrdSysSettings.return_value = mock_globalvars.sadrd_settings
        mock_dbops_instance.SADRD_Sys_Message.return_value = mock_globalvars.sadrd_ErrMessages
        mock_
