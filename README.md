 pytest --cov . test/ --cov-report html
===================================================== test session starts =====================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 83 items

test\test_APIHome.py .........                                                                                           [ 10%]
test\test_config.py .....................                                                                                [ 36%]
test\test_db.py .                                                                                                        [ 37%]
test\test_globalvars.py .                                                                                                [ 38%]
test\Entities\test_Customentities.py ....                                                                                [ 43%]
test\Entities\test_dbormschemas.py .........                                                                             [ 54%]
test\Services\test_APIResponse.py .......                                                                                [ 62%]
test\Services\test_Auth.py ........                                                                                      [ 72%]
test\Services\test_CustomException.py ..                                                                                 [ 74%]
test\Services\test_dboperations.py ...........                                                                           [ 87%]
test\Services\test_fileoperations.py ....                                                                                [ 92%]
test\Services\test_logoperations.py ...                                                                                  [ 96%]
test\Services\test_parentparser.py ..F                                                                                   [100%]

========================================================== FAILURES =========================================================== 
_____________________________ TestParentParser.test_parentparser_generate_sadrd_report_no_refresh _____________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_generate_sadrd_report_no_refresh>
mock_execute_view = <MagicMock name='executeView_GetDetails' id='1690121038288'>
mock_dbops_constructor = <MagicMock name='dboperations' id='1690121301584'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.sdp.executeView_GetDetails')
    def test_parentparser_generate_sadrd_report_no_refresh(self, mock_execute_view, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_dbops_instance.SadrdSysSettings.return_value = [
            MagicMock(settingName="IsRefreshUVAndVPA", settingValue="N"),
            MagicMock(settingName="Valid_Company", settingValue="test_company"),
            MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")
        ]
        mock_execute_view.return_value = pd.DataFrame(columns=range(5))
        parentparser({"action1": ["file1.txt"]}, "/input/dir/", "generateSADRDReport", 2025)
>       mock_execute_view.assert_called()

test\Services\test_parentparser.py:97:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='executeView_GetDetails' id='1690121038288'>

    def assert_called(self):
        """assert that the mock was called at least once
        """
        if self.call_count == 0:
            msg = ("Expected '%s' to have been called." %
                   (self._mock_name or 'mock'))
>           raise AssertionError(msg)
E           AssertionError: Expected 'executeView_GetDetails' to have been called.

C:\Program Files\Python39\lib\unittest\mock.py:876: AssertionError
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
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_generate_sadrd_report_no_refresh - AssertionError: Expected 'executeView_GetDetails' to have been called.
========================================== 1 failed, 82 passed, 10 warnings in 3.75s ========================================== 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
