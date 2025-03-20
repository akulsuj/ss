 pytest --cov . test/ --cov-report html
===================================================== test session starts =====================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 84 items

test\test_APIHome.py .........                                                                                           [ 10%]
test\test_config.py .....................                                                                                [ 35%]
test\test_db.py .                                                                                                        [ 36%] 
test\test_globalvars.py .                                                                                                [ 38%] 
test\Entities\test_Customentities.py ....                                                                                [ 42%]
test\Entities\test_dbormschemas.py .........                                                                             [ 53%]
test\Services\test_APIResponse.py .......                                                                                [ 61%]
test\Services\test_Auth.py ........                                                                                      [ 71%]
test\Services\test_CustomException.py ..                                                                                 [ 73%]
test\Services\test_dboperations.py ...........                                                                           [ 86%]
test\Services\test_fileoperations.py ....                                                                                [ 91%]
test\Services\test_logoperations.py ...                                                                                  [ 95%]
test\Services\test_parentparser.py .F.F                                                                                  [100%]

========================================================== FAILURES =========================================================== 
________________________________ TestParentParser.test_parentparser_ftcgrossup_file_error_e026 ________________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_ftcgrossup_file_error_e026>
mock_download = <MagicMock name='DownloadServerFilesToLoad' id='2103868119408'>
mock_dbops_constructor = <MagicMock name='dboperations' id='2103868135600'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_ftcgrossup_file_error_e026(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='N')]
        mock_globalvars.sadrd_ErrMessages = []
        mock_globalvars.fileErrorMessages = "E026, Error Message"
        mock_dbops_instance.SadrdSysSettings.return_value = mock_globalvars.sadrd_settings
        mock_dbops_instance.SADRD_Sys_Message.return_value = mock_globalvars.sadrd_ErrMessages
>       mock_
E       NameError: name 'mock_' is not defined

test\Services\test_parentparser.py:135: NameError
__________________________________ TestParentParser.test_parentparser_qualpctftc_file_error ___________________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_qualpctftc_file_error>
mock_download = <MagicMock name='DownloadServerFilesToLoad' id='2103866813840'>
mock_dbops_constructor = <MagicMock name='dboperations' id='2103865970112'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_qualpctftc_file_error(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='N')]
        mock_globalvars.sadrd_ErrMessages = []
        mock_globalvars.fileErrorMessages = "Error Message"
        mock_dbops_instance.SadrdSysSettings.return_value = mock_globalvars.sadrd_settings
        mock_dbops_instance.SADRD_Sys_Message.return_value = mock_globalvars.sadrd_ErrMessages
        mock_dbops_instance.BuildErrorMessage.return_value = "Error Message"

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "QualPctFTC"
        Year = 2025
        mock_download.return_value = ["/path/to/inputdir/file1.txt"]
        mock_sdp.parseCusipQualFTCFile.return_value = None

        with self.assertRaises(CustomException.FileValidationException):
>           parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)
E           AssertionError: FileValidationException not raised

test\Services\test_parentparser.py:122: AssertionError
====================================================== warnings summary ======================================================= 
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

=================================================== short test summary info =================================================== 
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_ftcgrossup_file_error_e026 - NameError: name 'mock_' is not defined
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_qualpctftc_file_error - AssertionError: FileValidationException not raised
========================================== 2 failed, 82 passed, 10 warnings in 3.37s ========================================== 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
