import unittest
from unittest.mock import MagicMock, patch
import sys
import os
from Services import CustomException

sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '..', '..', 'Services')))

mock_dbops = MagicMock(name='dboperations')
mock_sdp = MagicMock(name='SADRD_Dataparser')
mock_fiops = MagicMock(name='fileoperations')
mock_globalvars = MagicMock(name='globalvars')

sys.modules['Services.dboperations'] = mock_dbops
sys.modules['Services.SADRD_Dataparser'] = mock_sdp
sys.modules['Services.fileoperations'] = mock_fiops
sys.modules['globalvars'] = mock_globalvars
sys.modules['Services.CustomException'] = CustomException

class TestParentParser(unittest.TestCase):
    # ... (setUp and other test cases from previous examples)

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_download_error(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_download.side_effect = Exception("Download Error")

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "FTCGrossup"
        Year = 2025

        with self.assertRaises(CustomException.FileValidationException) as context:
            parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)
        self.assertEqual(str(context.exception), "Download Error")

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_parsing_error(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_download.return_value = ["/path/to/inputdir/file1.txt"]
        mock_sdp.parseJHFundsFTCGrossupFile.side_effect = Exception("Parsing Error")

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "FTCGrossup"
        Year = 2025

        with self.assertRaises(CustomException.FileValidationException) as context:
            parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)
        self.assertEqual(str(context.exception), "Parsing Error")

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_db_error(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_download.return_value = ["/path/to/inputdir/file1.txt"]
        mock_dbops_instance.executeSADRD_SP.side_effect = Exception("DB Error")

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "FTCGrossup"
        Year = 2025

        with self.assertRaises(CustomException.DatabaseOperationException) as context:
            parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)
        self.assertEqual(str(context.exception), "DB Error")

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_refresh_uv_vpa(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='Y')]
        mock_download.return_value = ["/path/to/inputdir/file1.txt"]

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "FTCGrossup"
        Year = 2025
        mock_dbops_instance.executeUV_SP.return_value = None
        mock_dbops_instance.executeVPA_SP.return_value = None
        mock_dbops_instance.insert_actionLog.return_value = None

        result = parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)
        self.assertEqual(result.status, "Success")

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_empty_files(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_download.return_value = []

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "FTCGrossup"
        Year = 2025

        result = parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)
        self.assertEqual(result.status, "Success")
        self.assertEqual(result.message, "No files found to process.")
