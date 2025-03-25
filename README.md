import unittest
from unittest.mock import patch, MagicMock, call
import pandas as pd
from sqlalchemy.exc import SQLAlchemyError
from datetime import datetime
from Services.dboperations import dboperations, TranInprogress
import globalvars as gvar
import urllib
import Entities.dbormschemas as dbsch

gvar = MagicMock()
gvar.sqlconfig = MagicMock()
gvar.gconfig = MagicMock()
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

    def test_truncatetable(self):
        self.db_ops.truncatetable('test_table')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_SrcStaging_SchDAllPartsdata', 'Yes')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_FactsTemp_CntrlTotals_Input', 'nonQualFTC')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatescueNIR_SP(self, mock_read_sql):
        mock_read_sql.return_value = pd.DataFrame()
        self.db_ops.engine = MagicMock()
        gvar.gconfig.__getitem__.side_effect = {
            "DRIVER": "test_driver",
            "NIR_DATABASE_SERVER": "test_server",
            "NIR_DATABASE_NAME": "test_db",
            "NIR_DATABASE_AUTH_STRING": "test_auth",
            "SQLALCHEMYODBC": "%s"
        }
        self.db_ops.executeNIR_SP('sp_name', 2023, 'company')
