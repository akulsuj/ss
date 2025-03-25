import unittest
from unittest.mock import patch, MagicMock, call
import pandas as pd
from sqlalchemy.exc import SQLAlchemyError
from datetime import datetime
from Services.dboperations import dboperations, TranInprogress
import globalvars as gvar
import urllib
import Entities.dbormschemas as dbsch
import logging

class TestDbOperations(unittest.TestCase):

    @patch.object(dboperations, '__init__', return_value=None)
    @patch('Services.dboperations.sqlalchemy.create_engine')
    def setUp(self, mock_create_engine, mock_db_init):
        self.mock_engine = MagicMock()
        self.mock_connection = self.mock_engine.connect.return_value
        self.db_ops = dboperations()
        self.db_ops.engine = self.mock_engine
        self.db_ops.connection = self.mock_connection
        self.mock_session = MagicMock()
        self.db_ops.session = self.mock_session
        self.db_ops.metadata = MagicMock()

        # Replace with your actual DSN
        nir_dsn = "your_nir_dsn"
        sadrd_dsn = "your_sadrd_dsn"

        # Construct a valid URL string using the DSN
        nir_url = f"mssql+pyodbc://{nir_dsn}"
        sadrd_url = f"mssql+pyodbc://{sadrd_dsn}"

        # Mock gvar.gconfig as a dictionary with specific return values for keys
        gvar.gconfig = MagicMock()
        gvar.gconfig.__getitem__.side_effect = lambda key: {
            "DRIVER": "{test_driver}",
            "NIR_DATABASE_SERVER": "test_server",
            "NIR_DATABASE_NAME": "test_db",
            "NIR_DATABASE_AUTH_STRING": "test_auth",
            "SQLALCHEMYODBC": "mssql+pyodbc:///?odbc_connect=%s",
            "SADRD_DATABASE_SERVER": "sadrd_server",
            "SADRD_DATABASE_NAME": "sadrd_db",
            "CONNECTION_AUTH_STRING": "sadrd_auth"
        }[key]

        # Mock urllib.parse.quote_plus to return the valid url
        gvar.sqlconfig = MagicMock()
        gvar.sqlconfig.return_value = nir_url
        self.nir_url = nir_url
        self.sadrd_url = sadrd_url

        gvar.sadrd_ErrMessages = [MagicMock(MessageNumber='E019', Message='[YYYY]')]
        gvar.sadrd_settings = [MagicMock(settingName='test', settingValue='test')]
        gvar.scheduleEList = [MagicMock(Cusip='test', CusipName='test')]
        gvar.qualPctList = [MagicMock(Year=2023, Company='test', Cusip='test')]
        gvar.COMPLETED = 'completed'
        gvar.FAILED = 'failed'
        gvar.INPROGRESS = 'inprogress'
        gvar.user_id = 'test_user'

    # ... (rest of your test methods)
