 pytest --cov . test/ --cov-report html
================================================== test session starts ==================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 85 items

test\test_APIHome.py .........                                                                                     [ 10%]
test\test_config.py .....................                                                                          [ 35%]
test\test_db.py .                                                                                                  [ 36%] 
test\test_globalvars.py .                                                                                          [ 37%]
test\Entities\test_Customentities.py ....                                                                          [ 42%] 
test\Entities\test_dbormschemas.py .........                                                                       [ 52%]
test\Services\test_APIResponse.py .......                                                                          [ 61%]
test\Services\test_Auth.py ........                                                                                [ 70%]
test\Services\test_CustomException.py ..                                                                           [ 72%] 
test\Services\test_SADRD_CLI.py .......                                                                            [ 81%]
test\Services\test_dboperations.py .F.                                                                             [ 84%]
test\Services\test_fileoperations.py .......                                                                       [ 92%]
test\Services\test_logoperations.py ...                                                                            [ 96%]
test\Services\test_parentparser.py ...                                                                             [100%]

======================================================= FAILURES ======================================================== 
____________________________________________ TestDbOperations.test_loaddata _____________________________________________ 

self = <test.Services.test_dboperations.TestDbOperations testMethod=test_loaddata>
mock_to_sql = <MagicMock name='to_sql' id='2522968301424'>

    @patch('Services.dboperations.pd.DataFrame.to_sql')
    def test_loaddata(self, mock_to_sql):
        df = pd.DataFrame({'col1': [1, 2], 'col2': [3, 4]})
        self.db_ops.sysdlrec = MagicMock()
        self.db_ops.sysdlrec.dataload_key = 'test'
        self.db_ops.update_dataloadkey = MagicMock()
        self.db_ops.loaddata('SADRD_SrcStaging_SchDAllPartsdata', df)
        self.db_ops.loaddata('test_table', df)
>       mock_to_sql.assert_called()

test\Services\test_dboperations.py:78:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='to_sql' id='2522968301424'>

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
FAILED test/Services/test_dboperations.py::TestDbOperations::test_loaddata - AssertionError: Expected 'to_sql' to have been called.
======================================= 1 failed, 84 passed, 10 warnings in 4.24s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
