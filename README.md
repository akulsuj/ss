import unittest
from unittest.mock import MagicMock, patch, mock_open
import sys
import os
import datetime
import shutil
import pandas as pd
from Services import CustomException
import urllib

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

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.shutil.move')
    @patch('Services.parentparser.os.path.split')
    def test_copyFileToOutputFolder_move(self, mock_split, mock_move, mock_dbops_constructor):
        from Services.parentparser import copyFileToOutputFolder
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_split.return_value = ("/path/to", "file.txt")
        copyFileToOutputFolder("/input/dir/", "/input/dir/file.txt", "move")
        mock_move.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.shutil.copyfile')
    @patch('Services.parentparser.os.makedirs')
    @patch('Services.parentparser.os.path.exists')
    @patch('Services.parentparser.os.path.split')
    def test_copyFileToOutputFolder_copy(self, mock_split, mock_exists, mock_makedirs, mock_copyfile, mock_dbops_constructor):
        from Services.parentparser import copyFileToOutputFolder
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_split.return_value = ("/path/to", "file.txt")
        mock_exists.return_value = False
        copyFileToOutputFolder("/input/dir/", "/input/dir/file.txt", "yes")
        mock_makedirs.assert_called()
        mock_copyfile.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.shutil.copyfile')
    @patch('Services.parentparser.os.makedirs')
    @patch('Services.parentparser.os.path.exists')
    @patch('Services.parentparser.os.path.split')
    def test_copyFileToOutputFolder_copy_exists(self, mock_split, mock_exists, mock_makedirs, mock_copyfile, mock_dbops_constructor):
        from Services.parentparser import copyFileToOutputFolder
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_split.return_value = ("/path/to", "file.txt")
        mock_exists.return_value = True
        copyFileToOutputFolder("/input/dir/", "/input/dir/file.txt", "yes")
        mock_makedirs.assert_not_called()
        mock_copyfile.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.shutil.move', side_effect=Exception("Test Exception"))
    @patch('Services.parentparser.os.path.split')
    def test_copyFileToOutputFolder_move_exception(self, mock_split, mock_move, mock_dbops_constructor):
        from Services.parentparser import copyFileToOutputFolder
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_split.return_value = ("/path/to", "file.txt")
        with self.assertRaises(Exception):
            copyFileToOutputFolder("/input/dir/", "/input/dir/file.txt", "move")

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.shutil.copyfile', side_effect=Exception("Test Exception"))
    @patch('Services.parentparser.os.makedirs')
    @patch('Services.parentparser.os.path.exists')
    @patch('Services.parentparser.os.path.split')
    def test_copyFileToOutputFolder_copy_exception(self, mock_split, mock_exists, mock_makedirs, mock_copyfile, mock_dbops_constructor):
        from Services.parentparser import copyFileToOutputFolder
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_split.return_value = ("/path/to", "file.txt")
        mock_exists.return_value = False
        with self.assertRaises(Exception):
            copyFileToOutputFolder("/input/dir/", "/input/dir/file.txt", "yes")

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_qualpctftc(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='N')]
        mock_globalvars.sad
