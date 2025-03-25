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
test\Services\test_SADRD_CLI.py FFF.........                                                                       [ 84%]
test\Services\test_dboperations.py .                                                                               [ 85%]
test\Services\test_fileoperations.py .......                                                                       [ 93%]
test\Services\test_logoperations.py ...                                                                            [ 96%]
test\Services\test_parentparser.py ...                                                                             [100%]

======================================================= FAILURES ======================================================== 
__________________________________ TestSADRD_CLI.test_main_general_exception_handling ___________________________________ 

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_general_exception_handling>

    def test_main_general_exception_handling(self):
        with patch('sys.stdin', StringIO('123')):
>           with patch('SADRD_CLI.process_input', side_effect=Exception("Test General Exception")):

test\Services\test_SADRD_CLI.py:114:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1404: in __enter__
    original, local = self.get_original()
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <unittest.mock._patch object at 0x00000153F5511EB0>

    def get_original(self):
        target = self.getter()
        name = self.attribute

        original = DEFAULT
        local = False

        try:
            original = target.__dict__[name]
        except (AttributeError, KeyError):
            original = getattr(target, name, DEFAULT)
        else:
            local = True

        if name in _builtins and isinstance(target, ModuleType):
            self.create = True

        if not self.create and original is DEFAULT:
>           raise AttributeError(
                "%s does not have the attribute %r" % (target, name)
            )
E           AttributeError: <module 'SADRD_CLI' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\SADRD_CLI.py'> does not have the attribute 'process_input'

C:\Program Files\Python39\lib\unittest\mock.py:1377: AttributeError
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

self = <MagicMock name='exit' id='1460108967600'>, args = (1,), kwargs = {}
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

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_type_error_handling>

    def test_main_type_error_handling(self):
        with patch('sys.stdin', StringIO('123')):
>           with patch('SADRD_CLI.process_input', side_effect=TypeError("Test Type Error")):

test\Services\test_SADRD_CLI.py:104:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1404: in __enter__
    original, local = self.get_original()
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <unittest.mock._patch object at 0x00000153F5468400>

    def get_original(self):
        target = self.getter()
        name = self.attribute

        original = DEFAULT
        local = False

        try:
            original = target.__dict__[name]
        except (AttributeError, KeyError):
            original = getattr(target, name, DEFAULT)
        else:
            local = True

        if name in _builtins and isinstance(target, ModuleType):
            self.create = True

        if not self.create and original is DEFAULT:
>           raise AttributeError(
                "%s does not have the attribute %r" % (target, name)
            )
E           AttributeError: <module 'SADRD_CLI' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\SADRD_CLI.py'> does not have the attribute 'process_input'

C:\Program Files\Python39\lib\unittest\mock.py:1377: AttributeError
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
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_general_exception_handling - AttributeError: <module 'SADRD_CLI' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\SADRD_CLI.py'> does not ...
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_no_argument_no_stdin_tty - AssertionError: Expected 'exit' to be called once. Called 2 times.
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_type_error_handling - AttributeError: <module 'SADRD_CLI' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\SADRD_CLI.py'> does not ...
======================================= 3 failed, 85 passed, 10 warnings in 4.57s =======================================
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
