 pytest --cov . test/ --cov-report html
===================================================== test session starts =====================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 80 items

test\test_APIHome.py .........                                                                                           [ 11%]
test\test_config.py .....................                                                                                [ 37%]
test\test_db.py .                                                                                                        [ 38%] 
test\test_globalvars.py .                                                                                                [ 40%] 
test\Entities\test_Customentities.py ....                                                                                [ 45%]
test\Entities\test_dbormschemas.py .........                                                                             [ 56%]
test\Services\test_APIResponse.py .......                                                                                [ 65%]
test\Services\test_Auth.py F....                                                                                         [ 71%]
test\Services\test_CustomException.py ..                                                                                 [ 73%]
test\Services\test_SADRD_CLI.py .......                                                                                  [ 82%]
test\Services\test_dboperations.py .                                                                                     [ 83%]
test\Services\test_fileoperations.py .......                                                                             [ 92%]
test\Services\test_logoperations.py ...                                                                                  [ 96%]
test\Services\test_parentparser.py ...                                                                                   [100%]

========================================================== FAILURES =========================================================== 
_________________________________________ Test_Auth.test_token_required_invalid_token _________________________________________ 

self = <test.Services.test_Auth.Test_Auth testMethod=test_token_required_invalid_token>
mock_insert_actionLog = <MagicMock name='insert_actionLog' id='2359637972640'>
mock_decrypt_token = <MagicMock name='DecryptToken' id='2359637918672'>

    @patch('Services.Auth.DecryptToken')
    @patch('Services.dboperations.dboperations.insert_actionLog')
    def test_token_required_invalid_token(self, mock_insert_actionLog, mock_decrypt_token):
        mock_decrypt_token.side_effect = Exception('Invalid token')

        @auth.token_required
        def test_route():
            return jsonify({'message': 'Success'})

        with self.app.test_request_context(headers={'Authorization': 'Bearer invalid_token'}):
            response = test_route()
            self.assertEqual(response[1], 494)
            self.assertEqual(response[0].json, {'apirespmsg': 'Invalid Token!'})
>           mock_insert_actionLog.assert_called_once()

test\Services\test_Auth.py:92:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='insert_actionLog' id='2359637972640'>

    def assert_called_once(self):
        """assert that the mock was called only once.
        """
        if not self.call_count == 1:
            msg = ("Expected '%s' to have been called once. Called %s times.%s"
                   % (self._mock_name or 'mock',
                      self.call_count,
                      self._calls_repr()))
>           raise AssertionError(msg)
E           AssertionError: Expected 'insert_actionLog' to have been called once. Called 0 times.

C:\Program Files\Python39\lib\unittest\mock.py:886: AssertionError
---------------------------------------------------- Captured stderr call ----------------------------------------------------- 
root        : ERROR    Exception occurred in token_required() method :: Invalid token
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\Auth.py", line 100, in decorated
    decrypted_token = DecryptToken(data)
  File "C:\Program Files\Python39\lib\unittest\mock.py", line 1092, in __call__
    return self._mock_call(*args, **kwargs)
  File "C:\Program Files\Python39\lib\unittest\mock.py", line 1096, in _mock_call
    return self._execute_mock_call(*args, **kwargs)
  File "C:\Program Files\Python39\lib\unittest\mock.py", line 1151, in _execute_mock_call
    raise effect
Exception: Invalid token
------------------------------------------------------ Captured log call ------------------------------------------------------ 
ERROR    root:Auth.py:121 Exception occurred in token_required() method :: Invalid token
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\Auth.py", line 100, in decorated
    decrypted_token = DecryptToken(data)
  File "C:\Program Files\Python39\lib\unittest\mock.py", line 1092, in __call__
    return self._mock_call(*args, **kwargs)
  File "C:\Program Files\Python39\lib\unittest\mock.py", line 1096, in _mock_call
    return self._execute_mock_call(*args, **kwargs)
  File "C:\Program Files\Python39\lib\unittest\mock.py", line 1151, in _execute_mock_call
    raise effect
Exception: Invalid token
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
FAILED test/Services/test_Auth.py::Test_Auth::test_token_required_invalid_token - AssertionError: Expected 'insert_actionLog' to have been called once. Called 0 times.
========================================== 1 failed, 79 passed, 10 warnings in 3.55s ========================================== 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
