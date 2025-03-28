import unittest
from unittest.mock import patch, MagicMock, create_autospec
import pandas as pd
from datetime import datetime
import sqlalchemy
from sqlalchemy.orm import Session
import dboperations
from Entities import dbormschemas as dbsch
import globalvars as gvar

class TestDbOperations(unittest.TestCase):
    
    def setUp(self):
        # Setup globalvars and other test dependencies
        gvar.sqlconfig = "test_config"
        gvar.gconfig = {
            "SQLALCHEMYODBC": "DRIVER={driver};SERVER={server};DATABASE={database};%s",
            "DRIVER": "test_driver",
            "SADRD_DATABASE_SERVER": "test_server",
            "SADRD_DATABASE_NAME": "test_db",
            "CONNECTION_AUTH_STRING": "test_auth",
            "NIR_DATABASE_SERVER": "nir_server",
            "NIR_DATABASE_NAME": "nir_db",
            "NIR_DATABASE_AUTH_STRING": "nir_auth"
        }
        gvar.user_id = 1
        gvar.COMPLETED = "Completed"
        gvar.FAILED = "Failed"
        gvar.INPROGRESS = "InProgress"
        
        # Mock database components
        self.mock_engine = create_autospec(sqlalchemy.engine.Engine)
        self.mock_connection = MagicMock()
        self.mock_session = create_autospec(Session)
        self.mock_metadata = MagicMock()
        
        # Patch SQLAlchemy create_engine
        self.engine_patcher = patch('sqlalchemy.create_engine', return_value=self.mock_engine)
        self.mock_create_engine = self.engine_patcher.start()
        
        # Setup dboperations instance
        self.db_ops = dboperations.dboperations()
        self.db_ops.engine = self.mock_engine
        self.db_ops.connection = self.mock_connection
        self.db_ops.session = self.mock_session
        self.db_ops.metadata = self.mock_metadata
        
    def tearDown(self):
        self.engine_patcher.stop()
        
    def test_truncatetable_basic(self):
        """Test basic table truncation"""
        with patch.object(self.db_ops, 'insert_actionLog') as mock_action_log:
            self.db_ops.truncatetable('test_table')
            self.mock_connection.execution_options().execute.assert_called_once()
            mock_action_log.assert_not_called()
    
    def test_truncatetable_with_condition(self):
        """Test truncate with condition"""
        with patch.object(self.db_ops, 'insert_actionLog'):
            self.db_ops.truncatetable('SADRD_SrcStaging_SchDAllPartsdata', 'Yes')
            self.mock_connection.execution_options().execute.assert_called_once()
    
    def test_truncatetable_exception(self):
        """Test truncate with exception"""
        self.mock_connection.execution_options().execute.side_effect = Exception("Test error")
        with patch.object(self.db_ops, 'insert_actionLog') as mock_action_log:
            with self.assertRaises(Exception):
                self.db_ops.truncatetable('test_table')
            mock_action_log.assert_called_once()
    
    def test_loaddata_success(self):
        """Test successful data load"""
        test_df = pd.DataFrame({'col1': [1, 2], 'col2': ['a', 'b']})
        self.mock_engine.begin.return_value.__enter__.return_value = self.mock_connection
        
        with patch.object(self.db_ops, 'truncatetable'), \
             patch.object(self.db_ops, 'update_dataloadkey'):
            self.db_ops.loaddata('test_table', test_df)
            self.mock_connection.execute.assert_called()
    
    def test_loaddata_exception(self):
        """Test data load with exception"""
        test_df = pd.DataFrame({'col1': [1, 2], 'col2': ['a', 'b']})
        self.mock_engine.begin.return_value.__enter__.return_value = self.mock_connection
        self.mock_connection.execute.side_effect = Exception("Test error")
        
        with patch.object(self.db_ops, 'truncatetable'), \
             patch.object(self.db_ops, 'update_dataloadkey'), \
             patch.object(self.db_ops, 'insert_actionLog') as mock_action_log:
            with self.assertRaises(Exception):
                self.db_ops.loaddata('test_table', test_df)
            mock_action_log.assert_called_once()
    
    def test_insert_dataloadkey_success(self):
        """Test successful dataload key insertion"""
        self.mock_session.query().filter_by().first.return_value = None
        self.mock_session.query().scalar.return_value = None
        
        self.db_ops.insert_dataloadkey('test_key', '{}', 'test_source')
        self.mock_session.add.assert_called_once()
        self.mock_session.commit.assert_called_once()
    
    def test_insert_dataloadkey_tran_in_progress(self):
        """Test dataload key insertion with transaction in progress"""
        mock_rec = MagicMock()
        mock_rec.dataload_status = gvar.INPROGRESS
        self.mock_session.query().filter_by().first.return_value = mock_rec
        
        with self.assertRaises(dboperations.TranInprogress):
            self.db_ops.insert_dataloadkey('test_key', '{}', 'test_source')
    
    def test_update_dataloadkey(self):
        """Test updating dataload key"""
        mock_rec = MagicMock()
        self.db_ops.sysdlrec = mock_rec
        
        self.db_ops.update_dataloadkey('test_status', 'test_details')
        self.assertEqual(mock_rec.dataload_status, 'test_status')
        self.assertEqual(mock_rec.dataload_statusdetails, 'test_details')
        self.mock_session.commit.assert_called_once()
    
    def test_executeSADRD_SP_success(self):
        """Test executing stored procedure"""
        mock_connection = MagicMock()
        mock_cursor = MagicMock()
        mock_connection.cursor.return_value = mock_cursor
        self.mock_engine.raw_connection.return_value = mock_connection
        
        result = self.db_ops.executeSADRD_SP(['test_sp'])
        mock_cursor.execute.assert_called_once()
        mock_connection.commit.assert_called_once()
    
    def test_executeNIR_SP_success(self):
        """Test executing NIR stored procedure"""
        test_df = pd.DataFrame({'col1': [1, 2]})
        with patch('pandas.read_sql_query', return_value=test_df), \
             patch.object(self.db_ops, 'insert_actionLog'):
            result = self.db_ops.executeNIR_SP('test_sp', 2023, 'test_company')
            self.assertIsInstance(result, pd.DataFrame)
    
    def test_insert_actionLog_success(self):
        """Test inserting action log"""
        with patch('sqlalchemy.create_engine'), \
             patch('dboperations.dboperations.engine', self.mock_engine), \
             patch('dboperations.dboperations.connection', self.mock_connection):
            self.db_ops.insert_actionLog(1, 2023, 1, 'test', 'action', '2023-01-01', 'comment', 1)
            self.mock_session.add.assert_called_once()
            self.mock_session.commit.assert_called_once()
    
    def test_GetAllUsers(self):
        """Test getting all users"""
        mock_users = [MagicMock(), MagicMock()]
        self.mock_session.query().all.return_value = mock_users
        
        result = self.db_ops.GetAllUsers()
        self.assertEqual(len(result), 2)
    
    def test_UpdateUser_add(self):
        """Test adding new user"""
        self.db_ops.rolesRec = [MagicMock(Type='test_type', RoleID=1)]
        self.db_ops.usersRec = []
        
        input_data = {
            'userAction': 'add',
            'Name': 'test',
            'NetworkId': 'test_id',
            'Type': 'test_type',
            'isActive': 'true',
            'Email': 'test@test.com'
        }
        