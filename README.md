import unittest
from unittest.mock import patch, MagicMock
from flask import Flask

from Services.dboperations import dboperations

with patch("Services.dboperations"):
    from APIHome import mainapp


class TestFlaskAppConfig(unittest.TestCase):

    @patch('APIHome.GetImportTypes')
    def test_GetImportTypes(self, mock_GetImportTypes):
        mock_GetImportTypes()

        mock_GetImportTypes.assert_called_once()

    @patch('APIHome.ImportData')
    def test_ImportData(self, mock_ImportData):
        mock_ImportData()

        mock_ImportData.assert_called_once()

    @patch('APIHome.GetActionLogs')
    def test_GetActionLogs(self, mock_GetActionLogs):
        mock_GetActionLogs()

        mock_GetActionLogs.assert_called_once()

    @patch.dict('os.environ', {"FLASK_ENV": "production"})
    def test_production_config(self):
        mainapp.config.from_object("config.ProductionConfig")
        self.assertFalse(mainapp.config["DEBUG"] is False)

    @patch.dict('os.environ', {"FLASK_ENV": "stage"})
    def test_stage_config(self):
        mainapp.config.from_object("config.StageConfig")
        self.assertFalse(mainapp.config["DEBUG"] is False)


    @patch.dict('os.environ', {"FLASK_ENV": "test"})
    def test_test_config(self):
        mainapp.config.from_object("config.TestingConfig")
        self.assertTrue(mainapp.config["TESTING"])

    
    @patch.dict('os.environ', {"FLASK_ENV": "development"})
    def test_development_config(self):
        mainapp.config.from_object("config.DevelopmentConfig")
        self.assertTrue(mainapp.config["DEBUG"])


    @patch.dict('os.environ', {"FLASK_ENV": "local"})
    def test_local_config(self):
        mainapp.config.from_object("config.LocalConfig")
        self.assertTrue(mainapp.config["DEBUG"])

    def test_default_config(self):
        mainapp.config.from_object("config.LocalConfig")
        self.assertTrue(mainapp.config["DEBUG"])
