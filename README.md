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
        gvar.gconfig.__getitem__.return_value = "%s"
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
        self.db_ops.insert_actionLog = MagicMock()
        self.mock_connection.cursor.return_value.execute.side_effect = SQLAlchemyError('test')
        with self.assertRaises(SQLAlchemyError):
            self.db_ops.executeSADRD_SP(['sp_name'])


 pytest --cov . test/ --cov-report html
================================================== test session starts ==================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 89 items

test\test_APIHome.py .........                                                                                     [ 10%]
test\test_config.py .....................                                                                          [ 33%]
test\test_db.py .                                                                                                  [ 34%] 
test\test_globalvars.py .                                                                                          [ 35%]
test\Entities\test_Customentities.py ....                                                                          [ 40%]
test\Entities\test_dbormschemas.py .........                                                                       [ 50%]
test\Services\test_APIResponse.py .......                                                                          [ 58%]
test\Services\test_Auth.py ........                                                                                [ 67%]
test\Services\test_CustomException.py ..                                                                           [ 69%]
test\Services\test_SADRD_CLI.py .......                                                                            [ 77%]
test\Services\test_dboperations.py FF.FF..                                                                         [ 85%]
test\Services\test_fileoperations.py .......                                                                       [ 93%]
test\Services\test_logoperations.py ...                                                                            [ 96%]
test\Services\test_parentparser.py ...                                                                             [100%]

======================================================= FAILURES ======================================================== 
__________________________________________ TestDbOperations.test_executeNIR_SP __________________________________________ 

self = <Services.dboperations.dboperations object at 0x000001D5B57BDD30>, sp = 'sp_name', year = 2023, company = 'company'

    def executeNIR_SP(self, sp, year, company):
        logging.debug("In executeNIR_SP() method")
        try:
>           gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"]) 
E           TypeError: string indices must be integers

Services\dboperations.py:190: TypeError

During handling of the above exception, another exception occurred:

self = <Services.dboperations.dboperations object at 0x000001D5B57BDD30>, sp = 'sp_name', year = 2023, company = 'company'

    def executeNIR_SP(self, sp, year, company):
        logging.debug("In executeNIR_SP() method")
        try:
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"]) 
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)   
            self.connection = self.engine.connect()

            sql_query = "EXEC " + sp + " " + str(year) + ", '" + company +"'"
            df = pd.read_sql_query((sql_query), self.connection)
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "Annual Stmt - Sch D", 'executeNIR_SP()', str(datetime.now())[0:23], "dboperations - executeNIR_SP method executed", None)
            self.session.commit()
        except Exception as e:
            logging.exception("Exception occurred in executeNIR_SP() method :: " + str(e))
>           self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, 'Annual Stmt - Sch D', 'executeNIR_SP()', str(datetime.now())[0:23], ("Exception occurred in executeNIR_SP() - " + str(e)), None)

Services\dboperations.py:201:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <Services.dboperations.dboperations object at 0x000001D5B57BDD30>, month = 3, year = 2025, userId = ''
module = 'Annual Stmt - Sch D', action = 'executeNIR_SP()', actionDate = '2025-03-25 14:14:12.394'
comments = 'Exception occurred in executeNIR_SP() - string indices must be integers', dataload_id = None, showInUI = 0    

    def insert_actionLog(self, month, year, userId, module, action, actionDate, comments, dataload_id, showInUI = 0):     
        logging.debug("In insert_actionLog() method")
        try:
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)   
            self.connection = self.engine.connect()
            self.actionLogRec = dbsch.ActionLog(Month = month, Year = year, UserID = userId, Module = module, Action = action, ActionDate = actionDate, Comments = comments, Dataload_Id = dataload_id, ShowInUI = showInUI)
            self.session.add(self.actionLogRec)
            self.session.commit()
        except Exception as e:
            self.session.rollback()
            logging.exception("Exception occurred in insert_actionLog() method :: " + str(e))
            logger.error('Action Log Insert Failed: '+ str(e))
>           raise e

Services\dboperations.py:297:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <Services.dboperations.dboperations object at 0x000001D5B57BDD30>, month = 3, year = 2025, userId = ''
module = 'Annual Stmt - Sch D', action = 'executeNIR_SP()', actionDate = '2025-03-25 14:14:12.394'
comments = 'Exception occurred in executeNIR_SP() - string indices must be integers', dataload_id = None, showInUI = 0    

    def insert_actionLog(self, month, year, userId, module, action, actionDate, comments, dataload_id, showInUI = 0):     
        logging.debug("In insert_actionLog() method")
        try:
            self.params = gvar.sqlconfig
>           self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)   
E           TypeError: string indices must be integers

Services\dboperations.py:288: TypeError

During handling of the above exception, another exception occurred:

self = <test.Services.test_dboperations.TestDbOperations testMethod=test_executeNIR_SP>
mock_read_sql = <MagicMock name='read_sql' id='2017384515760'>

    @patch('Services.dboperations.pd.read_sql')
    def test_executeNIR_SP(self, mock_read_sql):
        mock_read_sql.return_value = pd.DataFrame()
        self.db_ops.engine = MagicMock()
>       self.db_ops.executeNIR_SP('sp_name', 2023, 'company')

test\Services\test_dboperations.py:125:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <Services.dboperations.dboperations object at 0x000001D5B57BDD30>, sp = 'sp_name', year = 2023, company = 'company'

    def executeNIR_SP(self, sp, year, company):
        logging.debug("In executeNIR_SP() method")
        try:
            gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"]) 
            self.params = gvar.sqlconfig
            self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)   
            self.connection = self.engine.connect()

            sql_query = "EXEC " + sp + " " + str(year) + ", '" + company +"'"
            df = pd.read_sql_query((sql_query), self.connection)
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, "Annual Stmt - Sch D", 'executeNIR_SP()', str(datetime.now())[0:23], "dboperations - executeNIR_SP method executed", None)
            self.session.commit()
        except Exception as e:
            logging.exception("Exception occurred in executeNIR_SP() method :: " + str(e))
            self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, 'Annual Stmt - Sch D', 'executeNIR_SP()', str(datetime.now())[0:23], ("Exception occurred in executeNIR_SP() - " + str(e)), None)
            logger.error('Error occured in executing SP: ' + sp + ';' + str(e))
            raise e
        finally:
>           gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
E           TypeError: string indices must be integers

Services\dboperations.py:205: TypeError
------------------------------------------------- Captured stderr call -------------------------------------------------- 
root        : ERROR    Exception occurred in executeNIR_SP() method :: string indices must be integers
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 190, in executeNIR_SP
    gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"])
TypeError: string indices must be integers
root        : ERROR    Exception occurred in insert_actionLog() method :: string indices must be integers
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 190, in executeNIR_SP
    gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"])
TypeError: string indices must be integers

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 288, in insert_actionLog
    self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
TypeError: string indices must be integers
Services.dboperations: ERROR    Action Log Insert Failed: string indices must be integers
--------------------------------------------------- Captured log call --------------------------------------------------- 
ERROR    root:dboperations.py:200 Exception occurred in executeNIR_SP() method :: string indices must be integers
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 190, in executeNIR_SP
    gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"])
TypeError: string indices must be integers
ERROR    root:dboperations.py:295 Exception occurred in insert_actionLog() method :: string indices must be integers      
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 190, in executeNIR_SP
    gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"])
TypeError: string indices must be integers

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 288, in insert_actionLog
    self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
TypeError: string indices must be integers
ERROR    Services.dboperations:dboperations.py:296 Action Log Insert Failed: string indices must be integers
_________________________________________ TestDbOperations.test_executeSADRD_SP _________________________________________ 

self = <test.Services.test_dboperations.TestDbOperations testMethod=test_executeSADRD_SP>

    def test_executeSADRD_SP(self):
        self.db_ops.engine = MagicMock()
        result = self.db_ops.executeSADRD_SP(['sp_name'])
        self.assertEqual(result, '')
        self.db_ops.insert_actionLog = MagicMock()
        self.mock_connection.cursor.return_value.execute.side_effect = SQLAlchemyError('test')
        with self.assertRaises(SQLAlchemyError):
>           self.db_ops.executeSADRD_SP(['sp_name'])
E           AssertionError: SQLAlchemyError not raised

test\Services\test_dboperations.py:119: AssertionError
_______________________________________ TestDbOperations.test_insert_dataloadkey ________________________________________ 

self = <Services.dboperations.dboperations object at 0x000001D5B5861FA0>, loadkey = 'loadkey', strJsonVal = '{}'
src = 'src', status = '', cycleDate = '', importedby = '', comments = ''

    def insert_dataloadkey(self, loadkey, strJsonVal, src, status = '', cycleDate = '', importedby = '', comments = ''):  
        logging.debug("In insert_dataloadkey() method")
        try:
            self.params = gvar.sqlconfig
>           self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)    
E           TypeError: string indices must be integers

Services\dboperations.py:105: TypeError

During handling of the above exception, another exception occurred:

self = <test.Services.test_dboperations.TestDbOperations testMethod=test_insert_dataloadkey>

    def test_insert_dataloadkey(self):
        self.mock_session.query().filter_by().first.return_value = None
        gvar.gconfig.__getitem__.return_value = "%s"
>       self.db_ops.insert_dataloadkey('loadkey', '{}', 'src')

test\Services\test_dboperations.py:85:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
Services\dboperations.py:124: in insert_dataloadkey
    self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, loadkey, 'insert_dataloadkey', str(datetime.now())[0:23], ("Exception occurred in insert_dataloadkey - " + str(e)), None)
Services\dboperations.py:297: in insert_actionLog
    raise e
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <Services.dboperations.dboperations object at 0x000001D5B5861FA0>, month = 3, year = 2025, userId = ''
module = 'loadkey', action = 'insert_dataloadkey', actionDate = '2025-03-25 14:14:12.952'
comments = 'Exception occurred in insert_dataloadkey - string indices must be integers', dataload_id = None, showInUI = 0 

    def insert_actionLog(self, month, year, userId, module, action, actionDate, comments, dataload_id, showInUI = 0):     
        logging.debug("In insert_actionLog() method")
        try:
            self.params = gvar.sqlconfig
>           self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)   
E           TypeError: string indices must be integers

Services\dboperations.py:288: TypeError
------------------------------------------------- Captured stderr call -------------------------------------------------- 
root        : ERROR    Exception occurred in insert_dataloadkey() method :: string indices must be integers
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 105, in insert_dataloadkey
    self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
TypeError: string indices must be integers
root        : ERROR    Exception occurred in insert_actionLog() method :: string indices must be integers
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 105, in insert_dataloadkey
    self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
TypeError: string indices must be integers

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 288, in insert_actionLog
    self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
TypeError: string indices must be integers
Services.dboperations: ERROR    Action Log Insert Failed: string indices must be integers
--------------------------------------------------- Captured log call ---------------------------------------------------
ERROR    root:dboperations.py:123 Exception occurred in insert_dataloadkey() method :: string indices must be integers    
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 105, in insert_dataloadkey
    self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
TypeError: string indices must be integers
ERROR    root:dboperations.py:295 Exception occurred in insert_actionLog() method :: string indices must be integers      
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 105, in insert_dataloadkey
    self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
TypeError: string indices must be integers

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\dboperations.py", line 288, in insert_actionLog
    self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params, fast_executemany=True)
TypeError: string indices must be integers
ERROR    Services.dboperations:dboperations.py:296 Action Log Insert Failed: string indices must be integers
____________________________________________ TestDbOperations.test_loaddata _____________________________________________ 

self = <test.Services.test_dboperations.TestDbOperations testMethod=test_loaddata>
mock_dataframe = <MagicMock name='DataFrame' id='2017384884736'>
mock_to_sql = <MagicMock name='to_sql' id='2017384949696'>

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
>       mock_to_sql.assert_called()

test\Services\test_dboperations.py:80:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='to_sql' id='2017384949696'>

    def assert_called(self):
        """assert that the mock was called at least once
        """
        if self.call_count == 0:
            msg = ("Expected '%s' to have been called." %
                   (self._mock_name or 'mock'))
>           raise AssertionError(msg)
E           AssertionError: Expected 'to_sql' to have been called.

C:\Program Files\Python39\lib\unittest\mock.py:876: AssertionError
=================================================== warnings summary ==================================================== 
venv\lib\site-packages\pandas\compat\numpy\__init__.py:10
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:10: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    _nlv = LooseVersion(_np_version)

venv\lib\site-packages\pandas\compat\numpy\__init__.py:11
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:11: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    np_version_under1p17 = _nlv < LooseVersion("1.17")

venv\lib\site-packages\pandas\compat\numpy\__init__.py:12
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:12: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    np_version_under1p18 = _nlv < LooseVersion("1.18")

venv\lib\site-packages\pandas\compat\numpy\__init__.py:13
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:13: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    _np_version_under1p19 = _nlv < LooseVersion("1.19")

venv\lib\site-packages\pandas\compat\numpy\__init__.py:14
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\__init__.py:14: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    _np_version_under1p20 = _nlv < LooseVersion("1.20")

venv\lib\site-packages\setuptools\_distutils\version.py:337
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\setuptools\_distutils\version.py:337: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    other = LooseVersion(other)

venv\lib\site-packages\pandas\compat\numpy\function.py:120
venv\lib\site-packages\pandas\compat\numpy\function.py:120
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\compat\numpy\function.py:120: DeprecationWarning: distutils Version classes are deprecated. Use packaging.version instead.
    if LooseVersion(__version__) >= LooseVersion("1.17.0"):

venv\lib\site-packages\flask_sqlalchemy\__init__.py:14
venv\lib\site-packages\flask_sqlalchemy\__init__.py:14
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\flask_sqlalchemy\__init__.py:14: DeprecationWarning: '_app_ctx_stack' is deprecated and will be removed in Flask 2.3.
    from flask import _app_ctx_stack, abort, current_app, request

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html

---------- coverage: platform win32, python 3.9.13-final-0 -----------
Coverage HTML written to dir htmlcov

================================================ short test summary info ================================================
FAILED test/Services/test_dboperations.py::TestDbOperations::test_executeNIR_SP - TypeError: string indices must be integers
FAILED test/Services/test_dboperations.py::TestDbOperations::test_executeSADRD_SP - AssertionError: SQLAlchemyError not raised
FAILED test/Services/test_dboperations.py::TestDbOperations::test_insert_dataloadkey - TypeError: string indices must be integers
FAILED test/Services/test_dboperations.py::TestDbOperations::test_loaddata - AssertionError: Expected 'to_sql' to have been called.
======================================= 4 failed, 85 passed, 10 warnings in 5.06s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 

    @patch('Services.dboperations.pd.read_sql')
    def test_executeNIR_SP(self, mock_read_sql):
        mock_read_sql.return_value = pd.DataFrame()
        self.db_ops.engine = MagicMock()
        self.db_ops.executeNIR_SP('sp_name', 2023, 'company')
        
