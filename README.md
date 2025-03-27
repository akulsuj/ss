pytest --cov . test/ --cov-report html
===================================================== test session starts =====================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 90 items

test\test_APIHome.py FFFFFFFFFFFFFFFF                                                                                    [ 17%]
test\test_config.py .....................                                                                                [ 41%]
test\test_db.py .                                                                                                        [ 42%] 
test\test_globalvars.py .                                                                                                [ 43%] 
test\Entities\test_Customentities.py ....                                                                                [ 47%]
test\Entities\test_dbormschemas.py .........                                                                             [ 57%]
test\Services\test_APIResponse.py .......                                                                                [ 65%]
test\Services\test_Auth.py ....F...                                                                                      [ 74%]
test\Services\test_CustomException.py ..                                                                                 [ 76%] 
test\Services\test_SADRD_CLI.py .......                                                                                  [ 84%]
test\Services\test_dboperations.py .                                                                                     [ 85%]
test\Services\test_fileoperations.py .......                                                                             [ 93%]
test\Services\test_logoperations.py ...                                                                                  [ 96%]
test\Services\test_parentparser.py ...                                                                                   [100%]

========================================================== FAILURES =========================================================== 
______________________________________ APITestCase.test_authenticate_user_invalid_token _______________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_authenticate_user_invalid_token>
mock_token_required = <MagicMock name='token_required' id='2726451069664'>

    @patch('Services.Auth.token_required')
    def test_authenticate_user_invalid_token(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.GetAllUsers', return_value=[]):

test\test_APIHome.py:31:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
_________________________________________ APITestCase.test_authenticate_user_no_token _________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_authenticate_user_no_token>
mock_token_required = <MagicMock name='token_required' id='2726451696640'>

    @patch('Services.Auth.token_required')
    def test_authenticate_user_no_token(self, mock_token_required):
        mock_token_required.return_value = True
        response = self.app.get('/api/AuthenticateUser ')
>       self.assertEqual(response.status_code, 200)
E       AssertionError: 404 != 200

test\test_APIHome.py:25: AssertionError
_________________________________________ APITestCase.test_authenticate_user_success __________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_authenticate_user_success>
mock_token_required = <MagicMock name='token_required' id='2726451087344'>

    @patch('Services.Auth.token_required')
    def test_authenticate_user_success(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.GetAllUsers', return_value=[MagicMock(NetworkId='123', RoleId='1', Name='Test User', isActive=True, Email='test@example.com')]):

test\test_APIHome.py:15:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
______________________________________________ APITestCase.test_get_action_logs _______________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_get_action_logs>
mock_token_required = <MagicMock name='token_required' id='2726458183248'>

    @patch('Services.Auth.token_required')
    def test_get_action_logs(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.get_actionLog', return_value=[MagicMock(LogID=1, Month=1, Year=2023, UserID='123', Module='Test', Action='Test Action', ActionDate='2023-01-01', Comments='Test Comment', Dataload_Id='1')]):

test\test_APIHome.py:68:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
______________________________________________ APITestCase.test_get_import_types ______________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_get_import_types>
mock_token_required = <MagicMock name='token_required' id='2726458144848'>

    @patch('Services.Auth.token_required')
    def test_get_import_types(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='ImportType', settingValue='Type1', Description='Description1')]):

test\test_APIHome.py:39:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
_____________________________________________ APITestCase.test_get_qual_pct_data ______________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_get_qual_pct_data>
mock_token_required = <MagicMock name='token_required' id='2726458236496'>

    @patch('Services.Auth.token_required')
    def test_get_qual_pct_data(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.GetQualPctOverrideData', return_value=[MagicMock(Year='2023', Company='Test Company', Cusip='123456', QualPct=0.5)]):

test\test_APIHome.py:143:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
_____________________________________________ APITestCase.test_get_settings_data ______________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_get_settings_data>
mock_token_required = <MagicMock name='token_required' id='2726458621184'>

    @patch('Services.Auth.token_required')
    def test_get_settings_data(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='Setting1', settingValue='Value1', ShowInUI=True, Description='Description1', DataType='String')]):

test\test_APIHome.py:93:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
___________________________________________ APITestCase.test_get_valid_company_list ___________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_get_valid_company_list>
mock_token_required = <MagicMock name='token_required' id='2726458178960'>

    @patch('Services.Auth.token_required')
    def test_get_valid_company_list(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='Valid_Company', settingValue='Company1')]):

test\test_APIHome.py:118:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
____________________________________________ APITestCase.test_import_data_failure _____________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_import_data_failure>
mock_token_required = <MagicMock name='token_required' id='2726458182912'>

    @patch('Services.Auth.token_required')
    def test_import_data_failure(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='ServerFolderPath', settingValue='/path/to/server')]):

test\test_APIHome.py:57:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
____________________________________________ APITestCase.test_import_data_success _____________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_import_data_success>
mock_token_required = <MagicMock name='token_required' id='2726451071872'>

    @patch('Services.Auth.token_required')
    def test_import_data_success(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='ServerFolderPath', settingValue='/path/to/server')]):

test\test_APIHome.py:47:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
____________________________________________ APITestCase.test_update_qual_pct_data ____________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_update_qual_pct_data>
mock_token_required = <MagicMock name='token_required' id='2726451087152'>

    @patch('Services.Auth.token_required')
    def test_update_qual_pct_data(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.UpdateQualPctData', return_value='Success'):

test\test_APIHome.py:126:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
________________________________________ APITestCase.test_update_qual_pct_data_failure ________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_update_qual_pct_data_failure>
mock_token_required = <MagicMock name='token_required' id='2726458151312'>

    @patch('Services.Auth.token_required')
    def test_update_qual_pct_data_failure(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.UpdateQualPctData', return_value='Exists'):

test\test_APIHome.py:134:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
____________________________________________ APITestCase.test_update_settings_data ____________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_update_settings_data>
mock_token_required = <MagicMock name='token_required' id='2726458271344'>

    @patch('Services.Auth.token_required')
    def test_update_settings_data(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.UpdateSettingsData', return_value='Success'):

test\test_APIHome.py:101:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
________________________________________ APITestCase.test_update_settings_data_failure ________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_update_settings_data_failure>
mock_token_required = <MagicMock name='token_required' id='2726458523712'>

    @patch('Services.Auth.token_required')
    def test_update_settings_data_failure(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.UpdateSettingsData', return_value='Exists'):

test\test_APIHome.py:109:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
____________________________________________ APITestCase.test_update_user_failure _____________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_update_user_failure>
mock_token_required = <MagicMock name='token_required' id='2726458539936'>

    @patch('Services.Auth.token_required')
    def test_update_user_failure(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.UpdateUser ', return_value='Exists'):

test\test_APIHome.py:84:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
____________________________________________ APITestCase.test_update_user_success _____________________________________________ 

self = <test.test_APIHome.APITestCase testMethod=test_update_user_success>
mock_token_required = <MagicMock name='token_required' id='2726458229664'>

    @patch('Services.Auth.token_required')
    def test_update_user_success(self, mock_token_required):
        mock_token_required.return_value = True
>       with patch('dbops_obj.UpdateUser ', return_value='Success'):

test\test_APIHome.py:76:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
C:\Program Files\Python39\lib\unittest\mock.py:1388: in __enter__
    self.target = self.getter()
C:\Program Files\Python39\lib\unittest\mock.py:1563: in <lambda>
    getter = lambda: _importer(target)
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

target = 'dbops_obj'

    def _importer(target):
        components = target.split('.')
        import_path = components.pop(0)
>       thing = __import__(import_path)
E       ModuleNotFoundError: No module named 'dbops_obj'

C:\Program Files\Python39\lib\unittest\mock.py:1235: ModuleNotFoundError
_________________________________________ Test_Auth.test_token_required_invalid_token _________________________________________ 

args = (), kwargs = {}, data = 'Bearer test_token'

    @wraps(f)
    def decorated(*args, **kwargs):
        gvar.func = f.__name__
        gvar.user_id = ''
        gvar.user_name = ''
        gvar.user_id_short = ''
        gvar.user_ip_address = ''

        logging.debug("In token_required() method :: " + gvar.func)

        if (request.headers.__contains__('Authorization')):
            data = request.headers['Authorization']
            try:
>               decrypted_token = DecryptToken(data)

Services\Auth.py:100:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='DecryptToken' id='2726458250912'>, args = ('Bearer test_token',), kwargs = {}

    def __call__(self, /, *args, **kwargs):
        # can't use self in-case a function / method we are mocking uses self
        # in the signature
        self._mock_check_sig(*args, **kwargs)
        self._increment_mock_call(*args, **kwargs)
>       return self._mock_call(*args, **kwargs)

C:\Program Files\Python39\lib\unittest\mock.py:1092:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='DecryptToken' id='2726458250912'>, args = ('Bearer test_token',), kwargs = {}

    def _mock_call(self, /, *args, **kwargs):
>       return self._execute_mock_call(*args, **kwargs)

C:\Program Files\Python39\lib\unittest\mock.py:1096:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='DecryptToken' id='2726458250912'>, args = ('Bearer test_token',), kwargs = {}
effect = Exception('Invalid token')

    def _execute_mock_call(self, /, *args, **kwargs):
        # separate from _increment_mock_call so that awaited functions are
        # executed separately from their call, also AsyncMock overrides this method

        effect = self.side_effect
        if effect is not None:
            if _is_exception(effect):
>               raise effect
E               Exception: Invalid token

C:\Program Files\Python39\lib\unittest\mock.py:1151: Exception

During handling of the above exception, another exception occurred:

self = <test.Services.test_Auth.Test_Auth testMethod=test_token_required_invalid_token>
mock_insert_actionLog = <MagicMock name='insert_actionLog' id='2726457845504'>
mock_decrypt_token = <MagicMock name='DecryptToken' id='2726458250912'>

    @patch('Services.Auth.DecryptToken')
    @patch('Services.dboperations.dboperations.insert_actionLog')
    def test_token_required_invalid_token(self, mock_insert_actionLog, mock_decrypt_token):
        mock_decrypt_token.side_effect = Exception('Invalid token')

        @auth.token_required
        def test_route():
            return jsonify({'message': 'Success'})
    
        with self.app.test_request_context(headers={'Authorization': 'Bearer test_token'}):
>           response = test_route()

test\Services\test_Auth.py:98:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
Services\Auth.py:119: in decorated
    dbops_obj = dbops.dboperations()
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <Services.dboperations.dboperations object at 0x0000027ACD9729A0>, newsyssession = None

    def __init__(self, newsyssession=None):
        super().__init__()

        self.params = gvar.sqlconfig
>       self.engine = sqlalchemy.create_engine(gvar.gconfig["SQLALCHEMYODBC"] % self.params,fast_executemany=True)
E       TypeError: string indices must be integers

Services\dboperations.py:27: TypeError
====================================================== warnings summary ======================================================= 
venv\lib\site-packages\flask_sqlalchemy\__init__.py:14
venv\lib\site-packages\flask_sqlalchemy\__init__.py:14
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\flask_sqlalchemy\__init__.py:14: DeprecationWarning: '_app_ctx_stack' is deprecated and will be removed in Flask 2.3.
    from flask import _app_ctx_stack, abort, current_app, request

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

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html

---------- coverage: platform win32, python 3.9.13-final-0 -----------
Coverage HTML written to dir htmlcov

=================================================== short test summary info =================================================== 
FAILED test/test_APIHome.py::APITestCase::test_authenticate_user_invalid_token - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_authenticate_user_no_token - AssertionError: 404 != 200
FAILED test/test_APIHome.py::APITestCase::test_authenticate_user_success - ModuleNotFoundError: No module named 'dbops_obj'     
FAILED test/test_APIHome.py::APITestCase::test_get_action_logs - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_get_import_types - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_get_qual_pct_data - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_get_settings_data - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_get_valid_company_list - ModuleNotFoundError: No module named 'dbops_obj'        
FAILED test/test_APIHome.py::APITestCase::test_import_data_failure - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_import_data_success - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_update_qual_pct_data - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_update_qual_pct_data_failure - ModuleNotFoundError: No module named 'dbops_obj'  
FAILED test/test_APIHome.py::APITestCase::test_update_settings_data - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_update_settings_data_failure - ModuleNotFoundError: No module named 'dbops_obj'  
FAILED test/test_APIHome.py::APITestCase::test_update_user_failure - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/test_APIHome.py::APITestCase::test_update_user_success - ModuleNotFoundError: No module named 'dbops_obj'
FAILED test/Services/test_Auth.py::Test_Auth::test_token_required_invalid_token - TypeError: string indices must be integers    
========================================= 17 failed, 73 passed, 10 warnings in 11.21s ========================================= 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
