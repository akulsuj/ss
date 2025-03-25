 pytest --cov . test/ --cov-report html
================================================== test session starts ==================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 88 items

test\test_APIHome.py .........                                                                                     [ 10%]
test\test_config.py .....................                                                                          [ 34%]
test\test_db.py .                                                                                                  [ 35%] 
test\test_globalvars.py .                                                                                          [ 36%] 
test\Entities\test_Customentities.py ....                                                                          [ 40%]
test\Entities\test_dbormschemas.py .........                                                                       [ 51%]
test\Services\test_APIResponse.py .......                                                                          [ 59%]
test\Services\test_Auth.py ........                                                                                [ 68%]
test\Services\test_CustomException.py ..                                                                           [ 70%]
test\Services\test_SADRD_CLI.py .......                                                                            [ 78%]
test\Services\test_dboperations.py FF.F..                                                                          [ 85%]
test\Services\test_fileoperations.py .......                                                                       [ 93%]
test\Services\test_logoperations.py ...                                                                            [ 96%]
test\Services\test_parentparser.py ...                                                                             [100%]

======================================================= FAILURES ======================================================== 
__________________________________________ TestDbOperations.test_executeNIR_SP __________________________________________ 

self = <Services.dboperations.dboperations object at 0x0000021F96D08D90>, sp = 'sp_name', year = 2023, company = 'company'

    def executeNIR_SP(self, sp, year, company):
        logging.debug("In executeNIR_SP() method")
        try:
>           gvar.sqlconfig = urllib.parse.quote_plus("DRIVER=" + gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["NIR_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["NIR_DATABASE_NAME"] + gvar.gconfig["NIR_DATABASE_AUTH_STRING"]) 
E           TypeError: string indices must be integers

Services\dboperations.py:190: TypeError

During handling of the above exception, another exception occurred:

self = <Services.dboperations.dboperations object at 0x0000021F96D08D90>, sp = 'sp_name', year = 2023, company = 'company'

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

self = <Services.dboperations.dboperations object at 0x0000021F96D08D90>, month = 3, year = 2025, userId = ''
module = 'Annual Stmt - Sch D', action = 'executeNIR_SP()', actionDate = '2025-03-25 15:48:51.064'
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

self = <Services.dboperations.dboperations object at 0x0000021F96D08D90>, month = 3, year = 2025, userId = ''
module = 'Annual Stmt - Sch D', action = 'executeNIR_SP()', actionDate = '2025-03-25 15:48:51.064'
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
mock_read_sql = <MagicMock name='read_sql' id='2334697552144'>

    @patch('Services.dboperations.pd.read_sql')
    def test_executeNIR_SP(self, mock_read_sql):
        mock_read_sql.return_value = pd.DataFrame()
        self.db_ops.engine = MagicMock()
        gvar.gconfig.__getitem__.side_effect = {
            "DRIVER": "test_driver",
            "NIR_DATABASE_SERVER": "test_server",
            "NIR_DATABASE_NAME": "test_db",
            "NIR_DATABASE_AUTH_STRING": "test_auth",
            "SQLALCHEMYODBC": "%s"
        }
>       self.db_ops.executeNIR_SP('sp_name', 2023, 'company')

test\Services\test_dboperations.py:118:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <Services.dboperations.dboperations object at 0x0000021F96D08D90>, sp = 'sp_name', year = 2023, company = 'company'

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
        with patch.object(self.db_ops.connection.cursor(), 'execute', side_effect=SQLAlchemyError('test')):
            with self.assertRaises(SQLAlchemyError):
>               self.db_ops.executeSADRD_SP(['sp_name'])
E               AssertionError: SQLAlchemyError not raised

test\Services\test_dboperations.py:105: AssertionError
_______________________________________ TestDbOperations.test_insert_dataloadkey ________________________________________ 

self = <Services.dboperations.dboperations object at 0x0000021F96E37820>, loadkey = 'loadkey', strJsonVal = '{}'
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

test\Services\test_dboperations.py:74:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
Services\dboperations.py:124: in insert_dataloadkey
    self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, loadkey, 'insert_dataloadkey', str(datetime.now())[0:23], ("Exception occurred in insert_dataloadkey - " + str(e)), None)
Services\dboperations.py:297: in insert_actionLog
    raise e
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <Services.dboperations.dboperations object at 0x0000021F96E37820>, month = 3, year = 2025, userId = ''
module = 'loadkey', action = 'insert_dataloadkey', actionDate = '2025-03-25 15:48:51.438'
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
======================================= 3 failed, 85 passed, 10 warnings in 4.24s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
