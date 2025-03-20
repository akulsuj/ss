pytest --cov . test/ --cov-report html
================================================== test session starts ==================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 86 items

test\test_APIHome.py .........                                                                                     [ 10%]
test\test_config.py .....................                                                                          [ 34%]
test\test_db.py .                                                                                                  [ 36%] 
test\test_globalvars.py .                                                                                          [ 37%]
test\Entities\test_Customentities.py ....                                                                          [ 41%]
test\Entities\test_dbormschemas.py .........                                                                       [ 52%]
test\Services\test_APIResponse.py .......                                                                          [ 60%]
test\Services\test_Auth.py ........                                                                                [ 69%]
test\Services\test_CustomException.py ..                                                                           [ 72%]
test\Services\test_dboperations.py ...........                                                                     [ 84%]
test\Services\test_fileoperations.py ....F                                                                         [ 90%]
test\Services\test_logoperations.py ...                                                                            [ 94%]
test\Services\test_parentparser.py .....                                                                           [100%]

======================================================= FAILURES ======================================================== 
__________________________________ Test_FileOperations.test_getinpfilenames_toprocess ___________________________________ 

self = <test.Services.test_fileoperations.Test_FileOperations testMethod=test_getinpfilenames_toprocess>
mock_listdir = <MagicMock name='listdir' id='1958092684448'>, mock_path_join = <MagicMock name='join' id='1958092692304'> 

    @patch('os.path.join')
    @patch('os.listdir')
    def test_getinpfilenames_toprocess(self, mock_listdir, mock_path_join):
        folderpath = 'test_folder'
        inpLoadFolder = 'input_files'

        mock_path_join.side_effect = lambda *args: os.path.join(*args) # corrected line
        mock_listdir.return_value = [
            'file1.xlsx', 'file2.xlsm', 'file3.csv', 'file4.txt'
        ]

        expected_files = [
>           os.path.join('test_folder', 'input_files', 'file1.xlsx'),
            os.path.join('test_folder', 'input_files', 'file2.xlsm'),
            os.path.join('test_folder', 'input_files', 'file3.csv')
        ]

test\Services\test_fileoperations.py:44:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1092: in __call__
    return self._mock_call(*args, **kwargs)
C:\Program Files\Python39\lib\unittest\mock.py:1096: in _mock_call
    return self._execute_mock_call(*args, **kwargs)
C:\Program Files\Python39\lib\unittest\mock.py:1157: in _execute_mock_call
    result = effect(*args, **kwargs)
test\Services\test_fileoperations.py:38: in <lambda>
    mock_path_join.side_effect = lambda *args: os.path.join(*args) # corrected line
C:\Program Files\Python39\lib\unittest\mock.py:1092: in __call__
    return self._mock_call(*args, **kwargs)
E   RecursionError: maximum recursion depth exceeded
!!! Recursion detected (same locals & position)
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
FAILED test/Services/test_fileoperations.py::Test_FileOperations::test_getinpfilenames_toprocess - RecursionError: maximum recursion depth exceeded
======================================= 1 failed, 85 passed, 10 warnings in 4.25s =======================================
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
