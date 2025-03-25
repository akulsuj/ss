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
test\Services\test_SADRD_CLI.py FFF.F.......                                                                       [ 75%]
test\Services\test_dboperations.py ...........                                                                     [ 86%]
test\Services\test_fileoperations.py .......                                                                       [ 93%]
test\Services\test_logoperations.py ...                                                                            [ 96%]
test\Services\test_parentparser.py ...                                                                             [100%]

======================================================= FAILURES ======================================================== 
__________________________________ TestSADRD_CLI.test_main_general_exception_handling ___________________________________ 

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_general_exception_handling>

    def test_main_general_exception_handling(self):
        with patch('sys.stdin', StringIO('123')):
>           with patch('__main__.process_input', side_effect=Exception("Test General Exception")):

test\Services\test_SADRD_CLI.py:112:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1404: in __enter__
    original, local = self.get_original()
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <unittest.mock._patch object at 0x00000221EA47D460>

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
E           AttributeError: <module '__main__' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\venv\\Scripts\\pytest.exe\\__main__.py'> does not have the attribute 'process_input'

C:\Program Files\Python39\lib\unittest\mock.py:1377: AttributeError
___________________________________ TestSADRD_CLI.test_main_no_argument_no_stdin_tty ____________________________________ 

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_no_argument_no_stdin_tty>

    def test_main_no_argument_no_stdin_tty(self):
        with patch('sys.stdin.isatty', return_value=True):
            with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                with patch('sys.exit') as mock_exit:
                    main()
>                   self.assertIn("Please provide an input string.", mock_stdout.getvalue())
E                   AssertionError: 'Please provide an input string.' not found in '.\n'

test\Services\test_SADRD_CLI.py:98: AssertionError
------------------------------------------------- Captured stderr call -------------------------------------------------- 
usage: pytest [-h] [input_string]
pytest: error: unrecognized arguments: --cov test/ --cov-report html
______________________________________ TestSADRD_CLI.test_main_type_error_handling ______________________________________ 

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_type_error_handling>

    def test_main_type_error_handling(self):
        with patch('sys.stdin', StringIO('123')):
>           with patch('__main__.process_input', side_effect=TypeError("Test Type Error")):

test\Services\test_SADRD_CLI.py:103:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1404: in __enter__
    original, local = self.get_original()
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <unittest.mock._patch object at 0x00000221EA43AD30>

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
E           AttributeError: <module '__main__' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\venv\\Scripts\\pytest.exe\\__main__.py'> does not have the attribute 'process_input'

C:\Program Files\Python39\lib\unittest\mock.py:1377: AttributeError
__________________________________________ TestSADRD_CLI.test_main_with_stdin ___________________________________________ 

self = <test.Services.test_SADRD_CLI.TestSADRD_CLI testMethod=test_main_with_stdin>

    def test_main_with_stdin(self):
        with patch('sys.stdin', StringIO('abc\n')):
            with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
>               main()

test\Services\test_SADRD_CLI.py:90:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
test\Services\test_SADRD_CLI.py:33: in main
    args = parser.parse_args()
C:\Program Files\Python39\lib\argparse.py:1828: in parse_args
    self.error(msg % ' '.join(argv))
C:\Program Files\Python39\lib\argparse.py:2582: in error
    self.exit(2, _('%(prog)s: error: %(message)s\n') % args)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = ArgumentParser(prog='pytest', usage=None, description='Process input string.', formatter_class=<class 'argparse.HelpFormatter'>, conflict_handler='error', add_help=True)
status = 2, message = 'pytest: error: unrecognized arguments: --cov test/ --cov-report html\n'

    def exit(self, status=0, message=None):
        if message:
            self._print_message(message, _sys.stderr)
>       _sys.exit(status)
E       SystemExit: 2

C:\Program Files\Python39\lib\argparse.py:2569: SystemExit
------------------------------------------------- Captured stderr call -------------------------------------------------- 
usage: pytest [-h] [input_string]
pytest: error: unrecognized arguments: --cov test/ --cov-report html
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
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_general_exception_handling - AttributeError: <module '__main__' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\venv\\Scripts\\pytest.exe...
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_no_argument_no_stdin_tty - AssertionError: 'Please provide an input string.' not found in '.\n'
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_type_error_handling - AttributeError: <module '__main__' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\venv\\Scripts\\pytest.exe...
FAILED test/Services/test_SADRD_CLI.py::TestSADRD_CLI::test_main_with_stdin - SystemExit: 2
======================================= 4 failed, 94 passed, 10 warnings in 4.12s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
