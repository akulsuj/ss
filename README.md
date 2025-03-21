import unittest
from unittest.mock import MagicMock, patch
import sys
import os

sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '..', '..', 'Services')))

mock_dbops = MagicMock(name='dboperations')

sys.modules['Services.dboperations'] = mock_dbops

class TestParentParser(unittest.TestCase):

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.shutil.move')
    @patch('Services.parentparser.os.path.split')
    def test_copyFileToOutputFolder_move(self, mock_split, mock_move, mock_dbops_constructor):
        from Services.parentparser import copyFileToOutputFolder
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_split.return_value = ("/path/to", "file.txt")

        # Configure the mock to return a list with the expected setting
        mock_dbops_instance.SadrdSysSettings.return_value = [MagicMock(settingName='SADRD_Year', settingValue='2024')]

        copyFileToOutputFolder("/input/dir/", "/input/dir/file.txt", "move")
        mock_move.assert_called()
