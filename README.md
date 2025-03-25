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
test\Services\test_dboperations.py F.FF..                                                                          [ 85%]
test\Services\test_fileoperations.py .......                                                                       [ 93%]
test\Services\test_logoperations.py ...                                                                            [ 96%]
test\Services\test_parentparser.py ...                                                                             [100%]

======================================================= FAILURES ======================================================== 
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

test\Services\test_dboperations.py:120: AssertionError
_______________________________________ TestDbOperations.test_insert_dataloadkey ________________________________________ 

self = <Services.dboperations.dboperations object at 0x000001E8A164A100>, loadkey = 'loadkey', strJsonVal = '{}'
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
>       self.db_ops.insert_dataloadkey('loadkey', '{}', 'src')

test\Services\test_dboperations.py:86:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
Services\dboperations.py:124: in insert_dataloadkey
    self.insert_actionLog(datetime.now().month, datetime.now().year, gvar.user_id, loadkey, 'insert_dataloadkey', str(datetime.now())[0:23], ("Exception occurred in insert_dataloadkey - " + str(e)), None)
Services\dboperations.py:297: in insert_actionLog
    raise e
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <Services.dboperations.dboperations object at 0x000001E8A164A100>, month = 3, year = 2025, userId = ''
module = 'loadkey', action = 'insert_dataloadkey', actionDate = '2025-03-25 13:33:26.369'
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
mock_dataframe = <MagicMock name='DataFrame' id='2098651385376'>
mock_to_sql = <MagicMock name='to_sql' id='2098651941952'>

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

test\Services\test_dboperations.py:82:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='to_sql' id='2098651941952'>

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
FAILED test/Services/test_dboperations.py::TestDbOperations::test_executeSADRD_SP - AssertionError: SQLAlchemyError not raised
FAILED test/Services/test_dboperations.py::TestDbOperations::test_insert_dataloadkey - TypeError: string indices must be integers
FAILED test/Services/test_dboperations.py::TestDbOperations::test_loaddata - AssertionError: Expected 'to_sql' to have been called.
======================================= 3 failed, 85 passed, 10 warnings in 3.81s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
