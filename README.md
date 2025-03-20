 pytest --cov . test/ --cov-report html
================================================== test session starts ==================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 87 items

test\test_APIHome.py .........                                                                                     [ 10%]
test\test_config.py .....................                                                                          [ 34%]
test\test_db.py .                                                                                                  [ 35%]
test\test_globalvars.py .                                                                                          [ 36%]
test\Entities\test_Customentities.py ....                                                                          [ 41%]
test\Entities\test_dbormschemas.py .........                                                                       [ 51%]
test\Services\test_APIResponse.py .......                                                                          [ 59%]
test\Services\test_Auth.py ........                                                                                [ 68%]
test\Services\test_CustomException.py ..                                                                           [ 71%]
test\Services\test_dboperations.py ...........                                                                     [ 83%]
test\Services\test_fileoperations.py F.FFF.                                                                        [ 90%]
test\Services\test_logoperations.py ...                                                                            [ 94%]
test\Services\test_parentparser.py .....                                                                           [100%]

======================================================= FAILURES ======================================================== 
_______________________ Test_FileOperations.test_Downloadfilenames_toprocess_annual_schd_success ________________________ 

self = <test.Services.test_fileoperations.Test_FileOperations testMethod=test_Downloadfilenames_toprocess_annual_schd_success>
mock_is_open = <MagicMock name='is_open' id='2016322262688'>
mock_copyfile = <MagicMock name='copyfile' id='2016322274880'>
mock_listdir = <MagicMock name='listdir' id='2016322291072'>
mock_makedirs = <MagicMock name='makedirs' id='2016322303168'>, mock_exists = <MagicMock name='exists' id='2016322323456'>

    @patch('os.path.exists')
    @patch('os.makedirs')
    @patch('os.listdir')
    @patch('shutil.copyfile')
    @patch('Services.fileoperations.is_open')
    def test_Downloadfilenames_toprocess_annual_schd_success(self, mock_is_open, mock_copyfile, mock_listdir, mock_makedirs, mock_exists):
        serverInputFilesByAction = 'server_files'
        Inputdirpath = 'local_files'
        action = 'Annual Stmt - Sch D'
        year = '2023'
        mock_exists.return_value = True
        mock_listdir.return_value = ['2023JHUSASchD.csv']
        mock_is_open.return_value = False

        result = fo.Downloadfilenames_toprocess(serverInputFilesByAction, Inputdirpath, action, year)
>       self.assertEqual(result, ['local_files/2023JHUSASchD.csv'])
E       AssertionError: Lists differ: ['local_files\\2023JHUSASchD.csv'] != ['local_files/2023JHUSASchD.csv']
E       
E       First differing element 0:
E       'local_files\\2023JHUSASchD.csv'
E       'local_files/2023JHUSASchD.csv'
E       
E       - ['local_files\\2023JHUSASchD.csv']
E       ?              ^^
E       
E       + ['local_files/2023JHUSASchD.csv']
E       ?              ^

test\Services\test_fileoperations.py:72: AssertionError
________________________ Test_FileOperations.test_Downloadfilenames_toprocess_ftcgrossup_success ________________________ 

self = <test.Services.test_fileoperations.Test_FileOperations testMethod=test_Downloadfilenames_toprocess_ftcgrossup_success>
mock_is_open = <MagicMock name='is_open' id='2016323000400'>
mock_copyfile = <MagicMock name='copyfile' id='2016323020784'>
mock_listdir = <MagicMock name='listdir' id='2016323032880'>
mock_makedirs = <MagicMock name='makedirs' id='2016323049072'>, mock_exists = <MagicMock name='exists' id='2016323061168'>

    @patch('os.path.exists')
    @patch('os.makedirs')
    @patch('os.listdir')
    @patch('shutil.copyfile')
    @patch('Services.fileoperations.is_open')
    def test_Downloadfilenames_toprocess_ftcgrossup_success(self, mock_is_open, mock_copyfile, mock_listdir, mock_makedirs, mock_exists):
        serverInputFilesByAction = 'server_files'
        Inputdirpath = 'local_files'
        action = 'FTCGrossup'
        year = '2023'
        mock_exists.return_value = True
        mock_listdir.return_value = ['2023_Q1_FTCGrossup.xlsx', '2023_Q2_FTCGrossup.xlsx', '2023_Q3_FTCGrossup.xlsx', '2023_Q4_FTCGrossup.xlsx']
        mock_is_open.return_value = False

        result = fo.Downloadfilenames_toprocess(serverInputFilesByAction, Inputdirpath, action, year)
>       self.assertEqual(result, ['local_files/2023_Q1_FTCGrossup.xlsx', 'local_files/2023_Q2_FTCGrossup.xlsx', 'local_files/2023_Q3_FTCGrossup.xlsx', 'local_files/2023_Q4_FTCGrossup.xlsx'])
E       AssertionError: Lists differ: ['local_files\\2023_Q1_FTCGrossup.xlsx', 'local_files\[101 chars]lsx'] != ['local_files/2023_Q1_FTCGrossup.xlsx', 'local_files/2[97 chars]lsx']
E       
E       First differing element 0:
E       'local_files\\2023_Q1_FTCGrossup.xlsx'
E       'local_files/2023_Q1_FTCGrossup.xlsx'
E       
E       - ['local_files\\2023_Q1_FTCGrossup.xlsx',
E       ?              ^^
E       
E       + ['local_files/2023_Q1_FTCGrossup.xlsx',
E       ?              ^
E       
E       -  'local_files\\2023_Q2_FTCGrossup.xlsx',
E       ?              ^^
E       
E       +  'local_files/2023_Q2_FTCGrossup.xlsx',
E       ?              ^
E       
E       -  'local_files\\2023_Q3_FTCGrossup.xlsx',
E       ?              ^^
E       
E       +  'local_files/2023_Q3_FTCGrossup.xlsx',
E       ?              ^
E       
E       -  'local_files\\2023_Q4_FTCGrossup.xlsx']
E       ?              ^^
E       
E       +  'local_files/2023_Q4_FTCGrossup.xlsx']
E       ?              ^

test\Services\test_fileoperations.py:108: AssertionError
_________________________ Test_FileOperations.test_Downloadfilenames_toprocess_qualftc_success __________________________ 

self = <test.Services.test_fileoperations.Test_FileOperations testMethod=test_Downloadfilenames_toprocess_qualftc_success>
mock_is_open = <MagicMock name='is_open' id='2016322817472'>
mock_copyfile = <MagicMock name='copyfile' id='2016322041888'>
mock_listdir = <MagicMock name='listdir' id='2016321920544'>
mock_makedirs = <MagicMock name='makedirs' id='2016321249488'>, mock_exists = <MagicMock name='exists' id='2016321019808'>

    @patch('os.path.exists')
    @patch('os.makedirs')
    @patch('os.listdir')
    @patch('shutil.copyfile')
    @patch('Services.fileoperations.is_open')
    def test_Downloadfilenames_toprocess_qualftc_success(self, mock_is_open, mock_copyfile, mock_listdir, mock_makedirs, mock_exists):
        serverInputFilesByAction = 'server_files'
        Inputdirpath = 'local_files'
        action = 'QualPctFTC'
        year = '2023'
        mock_exists.return_value = True
        mock_listdir.return_value = ['2023QualFTC.xlsx']
        mock_is_open.return_value = False

        result = fo.Downloadfilenames_toprocess(serverInputFilesByAction, Inputdirpath, action, year)
>       self.assertEqual(result, ['local_files/2023QualFTC.xlsx'])
E       AssertionError: Lists differ: ['local_files\\2023QualFTC.xlsx'] != ['local_files/2023QualFTC.xlsx']
E       
E       First differing element 0:
E       'local_files\\2023QualFTC.xlsx'
E       'local_files/2023QualFTC.xlsx'
E       
E       - ['local_files\\2023QualFTC.xlsx']
E       ?              ^^
E       
E       + ['local_files/2023QualFTC.xlsx']
E       ?              ^

test\Services\test_fileoperations.py:90: AssertionError
_________________________ Test_FileOperations.test_Downloadfilenames_toprocess_wrong_file_type __________________________ 

self = <test.Services.test_fileoperations.Test_FileOperations testMethod=test_Downloadfilenames_toprocess_wrong_file_type>
mock_is_open = <MagicMock name='is_open' id='2016323302880'>
mock_copyfile = <MagicMock name='copyfile' id='2016323343744'>
mock_listdir = <MagicMock name='listdir' id='2016323327168'>
mock_makedirs = <MagicMock name='makedirs' id='2016323216384'>, mock_exists = <MagicMock name='exists' id='2016323388224'>

    @patch('os.path.exists')
    @patch('os.makedirs')
    @patch('os.listdir')
    @patch('shutil.copyfile')
    @patch('Services.fileoperations.is_open')
    def test_Downloadfilenames_toprocess_wrong_file_type(self, mock_is_open, mock_copyfile, mock_listdir, mock_makedirs, mock_exists):
        serverInputFilesByAction = 'server_files'
        Inputdirpath = 'local_files'
        action = 'Annual Stmt - Sch D'
        year = '2023'
        mock_exists.return_value = True
        mock_listdir.return_value = ['2023JHUSASchD.xlsx']
        mock_is_open.return_value = False
>       self.mock_dbops_
E       AttributeError: 'Test_FileOperations' object has no attribute 'mock_dbops_'

test\Services\test_fileoperations.py:141: AttributeError
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
FAILED test/Services/test_fileoperations.py::Test_FileOperations::test_Downloadfilenames_toprocess_annual_schd_success - AssertionError: Lists differ: ['local_files\\2023JHUSASchD.csv'] != ['local_files/2023JHUSASchD.csv']
FAILED test/Services/test_fileoperations.py::Test_FileOperations::test_Downloadfilenames_toprocess_ftcgrossup_success - AssertionError: Lists differ: ['local_files\\2023_Q1_FTCGrossup.xlsx', 'local_files\[101 chars]lsx'] != ['local_files...    
FAILED test/Services/test_fileoperations.py::Test_FileOperations::test_Downloadfilenames_toprocess_qualftc_success - AssertionError: Lists differ: ['local_files\\2023QualFTC.xlsx'] != ['local_files/2023QualFTC.xlsx']
FAILED test/Services/test_fileoperations.py::Test_FileOperations::test_Downloadfilenames_toprocess_wrong_file_type - AttributeError: 'Test_FileOperations' object has no attribute 'mock_dbops_'
======================================= 4 failed, 83 passed, 10 warnings in 3.74s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
