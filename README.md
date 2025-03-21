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
test\Services\test_dboperations.py ...........                                                                     [ 82%]
test\Services\test_fileoperations.py .......                                                                       [ 89%]
test\Services\test_logoperations.py ...                                                                            [ 93%]
test\Services\test_parentparser.py .....F                                                                          [100%]

======================================================= FAILURES ======================================================== 
_____________________________________ TestParentParser.test_parentparser_qualpctftc _____________________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_qualpctftc>
mock_download = <MagicMock name='DownloadServerFilesToLoad' id='2523383606768'>
mock_dbops_constructor = <MagicMock name='dboperations' id='2523384299280'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.fiops.DownloadServerFilesToLoad')
    def test_parentparser_qualpctftc(self, mock_download, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_globalvars.sadrd_settings = [MagicMock(settingName='IsRefreshUVAndVPA', settingValue='N')]
        mock_globalvars.sadrd_ErrMessages = []
        mock_globalvars.filesLoadedCount = 1
        mock_dbops_instance.SadrdSysSettings.return_value = mock_globalvars.sadrd_settings
        mock_dbops_instance.SADRD_Sys_Message.return_value = mock_globalvars.sadrd_ErrMessages

        serverInputFilesByAction = {}
        Inputdirpath = "/path/to/inputdir"
        import_type = "QualPctFTC"
        Year = 2025
        mock_download.return_value = ["/path/to/inputdir/file1.txt"]
        mock_sdp.parseCusipQualFTCFile.return_value = None
        mock_dbops_instance.BuildErrorMessage.return_value = "Success Message"
        mock_dbops_instance.executeSADRD_SP.return_value = None
        mock_dbops_instance.insert_actionLog.return_value = None

        result = parentparser(serverInputFilesByAction, Inputdirpath, import_type, Year)

>       self.assertEqual(result.status,)
E       TypeError: assertEqual() missing 1 required positional argument: 'second'

test\Services\test_parentparser.py:135: TypeError
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
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_qualpctftc - TypeError: assertEqual() missing 1 required positional argument: 'second'
======================================= 1 failed, 88 passed, 10 warnings in 4.12s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
