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

gvar = MagicMock()
gvar.sqlconfig = MagicMock()
gvar.gconfig = {"SQLALCHEMYODBC": "%s"}
gvar.sadrd_ErrMessages = [MagicMock(MessageNumber='E019', Message='[YYYY]')]
gvar.sadrd_settings = [MagicMock(settingName='test', settingValue='test')]
gvar.scheduleEList = [MagicMock(Cusip='test', CusipName='test')]
gvar.qualPctList = [MagicMock(Year=2023, Company='test', Cusip='test')]
gvar.COMPLETED = 'completed'
gvar.FAILED = 'failed'
gvar.INPROGRESS = 'inprogress'
gvar.user_id = 'test_user'

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
        mock_create_engine.return_value = self.mock_engine

    def test_truncatetable(self):
        # ... (Your existing test cases) ...

    @patch('Services.dboperations.pd.DataFrame.to_sql')
    @patch('Services.dboperations.pd.DataFrame')
    def test_loaddata(self, mock_dataframe, mock_to_sql):
        # ... (Your existing test cases) ...

    def test_insert_dataloadkey(self):
        # ... (Your existing test cases) ...

    def test_get_dataloadkey(self):
        # ... (Your existing test cases) ...

    def test_update_dataloadkey(self):
        # ... (Your existing test cases) ...

    def test_executeSADRD_SP(self):
        # ... (Your existing test cases) ...

    # Add more test cases to cover all branches and edge cases
    # Example: Test with empty lists, None values, etc.

    def test_insert_actionLog(self):
        self.db_ops.insert_actionLog(1, 2023, 'user123', 'moduleA', 'actionB', '2023-10-27', 'test comment', 123, 1)
        self.mock_session.add.assert_called_once()
        self.mock_session.commit.assert_called_once()
        self.mock_session.reset_mock()
        self.mock_session.commit.side_effect = SQLAlchemyError('test')
        with self.assertRaises(SQLAlchemyError):
            self.db_ops.insert_actionLog(1, 2023, 'user123', 'moduleA', 'actionB', '2023-10-27', 'test comment', 123, 1)

    def test_get_settings(self):
        self.db_ops.get_settings('test')
        self.mock_session.query().filter_by().first.assert_called_once()
        self.mock_session.query().filter_by().first.side_effect = SQLAlchemyError('test')
        self.db_ops.insert_actionLog = MagicMock()
        self.db_ops.get_settings('test')

    def test_get_err_messages(self):
        self.db_ops.get_err_messages('E019')
        self.mock_session.query().filter_by().first.assert_called_once()
        self.mock_session.query().filter_by().first.side_effect = SQLAlchemyError('test')
        self.db_ops.insert_actionLog = MagicMock()
        self.db_ops.get_err_messages('E019')

    def test_get_scheduleE(self):
        self.db_ops.get_scheduleE('test')
        self.mock_session.query().filter_by().first.assert_called_once()
        self.mock_session.query().filter_by().first.side_effect = SQLAlchemyError('test')
        self.db_ops.insert_actionLog = MagicMock()
        self.db_ops.get_scheduleE('test')

    def test_get_qualPct(self):
        self.db_ops.get_qualPct(2023, 'test')
        self.mock_session.query().filter_by().first.assert_called_once()
        self.mock_session.query().filter_by().first.side_effect = SQLAlchemyError('test')
        self.db_ops.insert_actionLog = MagicMock()
        self.db_ops.get_qualPct(2023, 'test')
