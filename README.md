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
            "VPA_DATABASE_AUTH_STRING": "test_vpa_auth",
            "SQLALCHEMYODBC": "DRIVER=%(driver)s;SERVER=%(server)s;DATABASE=%(database)s;UID=%(uid)s;PWD=%(pwd)s"
        }
        mock_globalvars.sqlconfig = {
            "driver": "test_driver",
            "server": "test_sadr_server",
            "database": "test_sadr_db",
            "uid": "test_uid",
            "pwd": "test_pwd"
        }

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    @patch('Services.parentparser.sdp.parseAnnualStmntFile')
    def test_parentparser_annual_stmt(self, mock_parse_annual, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName="Valid_Company", settingValue="test_company"), MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")]
        parentparser({"action1": ["file1.txt"]}, "/input/dir/", "Annual Stmt - Sch D", 2025)
        mock_parse_annual.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    @patch('Services.parentparser.sdp.parseCusipQualFTCFile')
    @patch('Services.parentparser.sdp.parseJHFundsFTCGrossupFile')
    def test_parentparser_qualpctftc(self, mock_parse_grossup, mock_parse_cusip, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_download.return_value = ["/input/dir/file1.txt"]
        parentparser({"action1": ["file1.txt"]}, "/input/dir/", "QualPctFTC", 2025)
        mock_parse_cusip.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    @patch('Services.parentparser.sdp.parseJHFundsFTCGrossupFile')
    def test_parentparser_ftcgrossup(self, mock_parse_grossup, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_download.return_value = ["/input/dir/file1.txt"]
        parentparser({"action1": ["file1.txt"]}, "/input/dir/", "FTCGrossup", 2025)
        mock_parse_grossup.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.sdp.executeView_GetDetails')
    @patch('Services.parentparser.sdp.Load_FundCusipDetails')
    @patch('Services.parentparser.sdp.LoadVPADataToSADRD')
    def test_parentparser_generate_sadrd_report(self, mock_load_vpa, mock_load_cusip, mock_execute_view, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName="IsRefreshUVAndVPA", settingValue="Y"), MagicMock(settingName="Valid_Company", settingValue="test_company"), MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")]
        mock_execute_view.return_value = pd.DataFrame()
        parentparser({"action1": ["file1.txt"]}, "/input/dir/", "generateSADRDReport", 2025)
        mock_execute_view.assert_called()
        mock_load_cusip.assert_called()
        mock_load_vpa.assert_called()

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.sdp.executeView_GetDetails')
    def test_parentparser_generate_sadrd_report_no_refresh(self, mock_execute_view, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName="IsRefreshUVAndVPA", settingValue="N"), MagicMock(settingName="Valid_Company", settingValue="test_company"), MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")]
        parentparser({"action1": ["file1.txt"]}, "/input/dir/", "generateSAD
