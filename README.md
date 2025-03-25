 pytest --cov . test/ --cov-report html
================================================== test session starts ==================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 98 items

test\test_APIHome.py .........                                                                                     [  9%]
test\test_config.py .....................                                                                          [ 30%]
test\test_db.py .                                                                                                  [ 31%]
test\test_globalvars.py .                                                                                          [ 32%]
test\Entities\test_Customentities.py ....                                                                          [ 36%]
test\Entities\test_dbormschemas.py .........                                                                       [ 45%]
test\Services\test_APIResponse.py .......                                                                          [ 53%]
test\Services\test_Auth.py ........                                                                                [ 61%]
test\Services\test_CustomException.py ..                                                                           [ 63%]
test\Services\test_SADRD_CLI.py FFF.........                                                                       [ 75%]
test\Services\test_dboperations.py ...........                                                                     [ 86%]
test\Services\test_fileoperations.py .......                                                                       [ 93%]
test\Services\test_logoperations.py ...                                                                            [ 96%]
test\Services\test_parentparser.py ...                                                                             [100%]

======================================================= FAILURES ======================================================== 
__________________________________ TestSADRD_CLI.test_main_general_exception_handling ___________________________________ 

thing = <module 'test.Services' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\test\\Services\\__init__.py'>    
comp = 'SADRD_CLI', import_path = 'test.Services.SADRD_CLI'

    def _dot_lookup(thing, comp, import_path):
        try:
>           return getattr(thing, comp)
E           AttributeError: module 'test.Services' has no attribute 'SADRD_CLI'

C:\Program Files\Python39\lib\unittest\mock.py:1226: AttributeError

During handling of the above exception, another exception occurred:

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_general_exception_handling>

    def test_main_general_exception_handling(self):
        with patch('sys.stdin', StringIO('123')):
>           with patch('test.Services.SADRD_CLI.process_input', side_effect=Exception("Test General Exception")):

test\Services\test_SADRD_CLI.py:114:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
C:\Program Files\Python39\lib\unittest\mock.py:1239: in _importer
    thing = _dot_lookup(thing, comp, import_path)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

thing = <module 'test.Services' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\test\\Services\\__init__.py'>    
comp = 'SADRD_CLI', import_path = 'test.Services.SADRD_CLI'

    def _dot_lookup(thing, comp, import_path):
        try:
            return getattr(thing, comp)
        except AttributeError:
>           __import__(import_path)
E           ModuleNotFoundError: No module named 'test.Services.SADRD_CLI'

C:\Program Files\Python39\lib\unittest\mock.py:1228: ModuleNotFoundError
___________________________________ TestSADRD_CLI.test_main_no_argument_no_stdin_tty ____________________________________ 

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_no_argument_no_stdin_tty>

    def test_main_no_argument_no_stdin_tty(self):
        with patch('sys.stdin.isatty', return_value=True):
            with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                with patch('sys.exit') as mock_exit:
                    with patch('sys.argv', ['SADRD_CLI.py']):
                        main()
                        self.assertIn("Please provide an input string.", mock_stdout.getvalue())
>                       mock_exit.assert_called_once_with(1)

test\Services\test_SADRD_CLI.py:100:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='exit' id='2549907083424'>, args = (1,), kwargs = {}
msg = "Expected 'exit' to be called once. Called 2 times.\nCalls: [call(1), call(1)]."

    def assert_called_once_with(self, /, *args, **kwargs):
        """assert that the mock was called exactly once and that that call was
        with the specified arguments."""
        if not self.call_count == 1:
            msg = ("Expected '%s' to be called once. Called %s times.%s"
                   % (self._mock_name or 'mock',
                      self.call_count,
                      self._calls_repr()))
>           raise AssertionError(msg)
E           AssertionError: Expected 'exit' to be called once. Called 2 times.
E           Calls: [call(1), call(1)].

C:\Program Files\Python39\lib\unittest\mock.py:918: AssertionError
______________________________________ TestSADRD_CLI.test_main_type_error_handling ______________________________________ 

thing = <module 'test.Services' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\test\\Services\\__init__.py'>    
comp = 'SADRD_CLI', import_path = 'test.Services.SADRD_CLI'

    def _dot_lookup(thing, comp, import_path):
        try:
>           return getattr(thing, comp)
E           AttributeError: module 'test.Services' has no attribute 'SADRD_CLI'

C:\Program Files\Python39\lib\unittest\mock.py:1226: AttributeError

During handling of the above exception, another exception occurred:

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_type_error_handling>

    def test_main_type_error_handling(self):
        with patch('sys.stdin', StringIO('123')):
>           with patch('test.Services.SADRD_CLI.process_input', side_effect=TypeError("Test Type Error")):

test\Services\test_SADRD_CLI.py:104:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
C:\Program Files\Python39\lib\unittest\mock.py:1239: in _importer
    thing = _dot_lookup(thing, comp, import_path)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

thing = <module 'test.Services' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\test\\Services\\__init__.py'>    
comp = 'SADRD_CLI', import_path = 'test.Services.SADRD_CLI'

    def _dot_lookup(thing, comp, import_path):
        try:
            return getattr(thing, comp)
        except AttributeError:
>           __import__(import_path)
E           ModuleNotFoundError: No module named 'test.Services.SADRD_CLI'

C:\Program Files\Python39\lib\unittest\mock.py:1228: ModuleNotFoundError
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
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_general_exception_handling - ModuleNotFoundError: No module named 'test.Services.SADRD_CLI'
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_no_argument_no_stdin_tty - AssertionError: Expected 'exit' to be called once. Called 2 times.
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_type_error_handling - ModuleNotFoundError: No module named 'test.Services.SADRD_CLI'
======================================= 3 failed, 95 passed, 10 warnings in 5.12s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
