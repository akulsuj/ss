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
        self.db_ops.truncatetable('test_table')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_SrcStaging_SchDAllPartsdata', 'Yes')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_FactsTemp_CntrlTotals_Input', 'nonQualFTC')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_FactsTemp_CntrlTotals_Input', 'QualFTC')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_FactsTemp_CntrlTotals_Input', 'FTCGrossup')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_FactsTemp_CntrlTotals_Input', 'GL')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_Factstemp_Exception', 'Sch D Part')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_Factstemp_Exception', 'QualPctFTC')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.truncatetable('SADRD_Factstemp_Exception', 'FTCGrossup')
        self.mock_connection.execution_options().execute.assert_called_once()
        self.mock_connection.execution_options().execute.reset_mock()
        self.db_ops.insert_actionLog = MagicMock()
        self.mock_connection.execution_options().execute.side_effect = SQLAlchemyError('test')
        with self.assertRaises(SQLAlchemyError):
            self.db_ops.truncatetable('test_table')

    @patch('Services.dboperations.pd.DataFrame.to_sql')
    @patch('Services.dboperations.pd.DataFrame')
    def test_loaddata(self, mock_dataframe, mock_to_sql):
        df = pd.DataFrame({'col1': [1, 2], 'col2': [3, 4]})
        mock_dataframe.return_value = df
        self.db_ops.sysdlrec = MagicMock()
        self.db_ops.sysdlrec.dataload_key = 'test'
        self.db_ops.update_dataloadkey = MagicMock()
        self.db_ops.loaddata('SADRD_SrcStaging_SchDAllPartsdata', df)
        self.db_ops.loaddata('test_table', df)
        mock_to_sql.assert_called()

    def test_insert_dataloadkey(self):
        self.mock_session.query().filter_by().first.return_value = None
        self.db_ops.insert_dataloadkey('loadkey', '{}', 'src')
        self.mock_session.add.assert_called_once()
        self.mock_session.commit.assert_called_once()
        self.mock_session.query().filter_by().first.return_value = MagicMock(dataload_status = gvar.INPROGRESS)
        self.db_ops.insert_actionLog = MagicMock()
        with self.assertRaises(TranInprogress):
            self.db_ops.insert_dataloadkey('loadkey', '{}', 'src')
        self.mock_session.query().filter_by().first.return_value = None
        self.mock_session.add.side_effect = SQLAlchemyError('test')
        with self.assertRaises(SQLAlchemyError):
            self.db_ops.insert_dataloadkey('loadkey', '{}', 'src')

    def test_get_dataloadkey(self):
        self.db_ops.get_dataloadkey('loadkey')
        self.mock_session.query().filter_by().first.assert_called_once()
        self.mock_session.query().filter_by().first.side_effect = SQLAlchemyError('test')
        self.db_ops.insert_actionLog = MagicMock()
        self.db_ops.get_dataloadkey('loadkey')

    def test_update_dataloadkey(self):
        self.db_ops.sysdlrec = MagicMock()
        self.db_ops.update_dataloadkey('status', 'details')
        self.mock_session.commit.assert_called_once()
        self.mock_session.commit.side_effect = SQLAlchemyError('test')
        with self.assertRaises(SQLAlchemyError):
            self.db_ops.update_dataloadkey('status', 'details')

    def test_executeSADRD_SP(self):
        self.db_ops.engine = MagicMock()
        result = self.db_ops.executeSADRD_SP(['sp_name'])
        self.assertEqual(result, '')

    def test_insert_actionLog(self):
        self.db_ops.insert_actionLog(1, 2023, 'user123', 'moduleA', 'actionB', '2023-10-27', 'test comment', 123, 1)
        self.mock_session.add.assert_called_once()
        self.mock_session.commit.assert_called_once()
        self.mock_session.reset_mock()
        self.mock_session.commit.side_effect = SQLAlchemyError('test')
        with self.assertRaises(SQLAlchemyError):
            self.db_ops.insert_actionLog(1, 2023, 'user123', 'moduleA', 'actionB', '2023-10-27', 'test comment', 123, 1)

    def test_get_settings(self):
