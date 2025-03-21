 pytest --cov . test/ --cov-report html
================================================== test session starts ==================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 84 items

test\test_APIHome.py .........                                                                                     [ 10%]
test\test_config.py .....................                                                                          [ 35%]
test\test_db.py .                                                                                                  [ 36%] 
test\test_globalvars.py .                                                                                          [ 38%]
test\Entities\test_Customentities.py ....                                                                          [ 42%]
test\Entities\test_dbormschemas.py .........                                                                       [ 53%]
test\Services\test_APIResponse.py .......                                                                          [ 61%]
test\Services\test_Auth.py ........                                                                                [ 71%]
test\Services\test_CustomException.py ..                                                                           [ 73%]
test\Services\test_dboperations.py ...........                                                                     [ 86%]
test\Services\test_fileoperations.py .......                                                                       [ 95%]
test\Services\test_logoperations.py ...                                                                            [ 98%]
test\Services\test_parentparser.py F                                                                               [100%]

======================================================= FAILURES ======================================================== 
___________________________________ TestParentParser.test_copyFileToOutputFolder_move ___________________________________ 

thing = <module 'Services' from 'C:\\Sujith\\Projects\\SADRD\\FinanceIT_SADRD\\API\\Services\\__init__.py'>
comp = 'parentparser', import_path = 'Services.parentparser'

    def _dot_lookup(thing, comp, import_path):
        try:
>           return getattr(thing, comp)
E           AttributeError: module 'Services' has no attribute 'parentparser'

C:\Program Files\Python39\lib\unittest\mock.py:1226: AttributeError

During handling of the above exception, another exception occurred:
C:\Program Files\Python39\lib\unittest\mock.py:1333: in patched
    with self.decoration_helper(patched,
C:\Program Files\Python39\lib\contextlib.py:119: in __enter__
    return next(self.gen)
C:\Program Files\Python39\lib\unittest\mock.py:1315: in decoration_helper
    arg = exit_stack.enter_context(patching)
C:\Program Files\Python39\lib\contextlib.py:448: in enter_context
    result = _cm_type.__enter__(cm)
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
C:\Program Files\Python39\lib\unittest\mock.py:1239: in _importer
    thing = _dot_lookup(thing, comp, import_path)
C:\Program Files\Python39\lib\unittest\mock.py:1228: in _dot_lookup
    __import__(import_path)
Services\parentparser.py:10: in <module>
    import Services.SADRD_Dataparser   as sdp
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

    import json
    from datetime import datetime

    import pandas as pd , datetime , os

    import urllib

    from Services import dboperations as dbops
    import globalvars as gvar
    import logging
    import re
    import stringcase

    dbops_obj = dbops.dboperations()
    log = logging.getLogger(__name__)
    gvar.sadrd_settings = dbops_obj.SadrdSysSettings()
    gvar.sadrd_ErrMessages = dbops_obj.SADRD_Sys_Message()

    dataSettingYear = [x for x in gvar.sadrd_settings if x.settingName == 'SADRD_Year']
>   gvar.sadrdYear = int(dataSettingYear[0].settingValue)
E   IndexError: list index out of range

Services\SADRD_Dataparser.py:20: IndexError
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
FAILED test/Services/test_parentparser.py::TestParentParser::test_copyFileToOutputFolder_move - IndexError: list index out of range
======================================= 1 failed, 83 passed, 10 warnings in 6.44s ======================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
