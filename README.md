import unittest
from unittest.mock import patch, MagicMock
from APIHome import mainapp
import json

class APITestCase(unittest.TestCase):

    def setUp(self):
        self.app = mainapp.test_client()
        self.app.testing = True

    @patch('Services.Auth.token_required')
    def test_authenticate_user_success(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.GetAllUsers', return_value=[MagicMock(NetworkId='123', RoleId='1', Name='Test User', isActive=True, Email='test@example.com')]):
            with patch('dbops_obj.SadrdSysSettings', return_value=[]):
                response = self.app.get('/api/AuthenticateUser ', headers={'Authorization': 'Bearer valid_token'})
                self.assertEqual(response.status_code, 200)
                self.assertIn('authenticated', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_authenticate_user_no_token(self, mock_token_required):
        mock_token_required.return_value = True
        response = self.app.get('/api/AuthenticateUser ')
        self.assertEqual(response.status_code, 200)
        self.assertFalse(json.loads(response.data)['authenticated'])

    @patch('Services.Auth.token_required')
    def test_authenticate_user_invalid_token(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.GetAllUsers', return_value=[]):
            response = self.app.get('/api/AuthenticateUser ', headers={'Authorization': 'Bearer invalid_token'})
            self.assertEqual(response.status_code, 200)
            self.assertFalse(json.loads(response.data)['authenticated'])

    @patch('Services.Auth.token_required')
    def test_get_import_types(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='ImportType', settingValue='Type1', Description='Description1')]):
            response = self.app.get('/api/GetImportTypes')
            self.assertEqual(response.status_code, 200)
            self.assertIn('ImportTypesData', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_import_data_success(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='ServerFolderPath', settingValue='/path/to/server')]):
            with patch('Services.parentparser') as mock_parser:
                mock_parser.return_value = MagicMock(status='Success', message='Import successful')
                response = self.app.post('/api/ImportData', data={'year': '2023', 'importType': 'Type1'})
                self.assertEqual(response.status_code, 200)
                self.assertIn('status', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_import_data_failure(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='ServerFolderPath', settingValue='/path/to/server')]):
            with patch('Services.parentparser') as mock_parser:
                mock_parser.return_value = MagicMock(status='Failure', message='Import failed')
                response = self.app.post('/api/ImportData', data={'year': '2023', 'importType': 'Type1'})
                self.assertEqual(response.status_code, 200)
                self.assertIn('status', json.loads(response.data))
                self.assertEqual(json.loads(response.data)['status'], 'Failure')

    @patch('Services.Auth.token_required')
    def test_get_action_logs(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.get_actionLog', return_value=[MagicMock(LogID=1, Month=1, Year=2023, UserID='123', Module='Test', Action='Test Action', ActionDate='2023-01-01', Comments='Test Comment', Dataload_Id='1')]):
            response = self.app.get('/api/GetActionLogs')
            self.assertEqual(response.status_code, 200)
            self.assertIn('ActionLogsData', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_update_user_success(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.UpdateUser ', return_value='Success'):
            response = self.app.post('/api/UpdateUser ', data={'Name': 'Test User', 'userAction': 'add'})
            self.assertEqual(response.status_code, 200)
            self.assertIn('status', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_update_user_failure(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.UpdateUser ', return_value='Exists'):
            response = self.app.post('/api/UpdateUser ', data={'Name': 'Test User', 'userAction': 'add'})
            self.assertEqual(response.status_code, 200)
            self.assertIn('status', json.loads(response.data))
            self.assertEqual(json.loads(response.data)['status'], 'Exists')

    @patch('Services.Auth.token_required')
    def test_get_settings_data(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='Setting1', settingValue='Value1', ShowInUI=True, Description='Description1', DataType='String')]):
            response = self.app.get('/api/GetSettingsData')
            self.assertEqual(response.status_code, 200)
            self.assertIn('SettingsData', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_update_settings_data(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.UpdateSettingsData', return_value='Success'):
            response = self.app.post('/api/UpdateSettingsData', data={'settingName': 'Setting1', 'settingValue': 'NewValue', 'userAction': 'update'})
            self.assertEqual(response.status_code, 200)
            self.assertIn('status', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_update_settings_data_failure(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.UpdateSettingsData', return_value='Exists'):
            response = self.app.post('/api/UpdateSettingsData', data={'settingName': 'Setting1', 'settingValue': 'NewValue', 'userAction': 'update'})
            self.assertEqual(response.status_code, 200)
            self.assertIn('status', json.loads(response.data))
            self.assertEqual(json.loads(response.data)['status'], 'Exists')

    @patch('Services.Auth.token_required')
    def test_get_valid_company_list(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.SadrdSysSettings', return_value=[MagicMock(settingName='Valid_Company', settingValue='Company1')]):
            response = self.app.get('/api/GetValidCompanyList')
            self.assertEqual(response.status_code, 200)
            self.assertIn('CompanyList', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_update_qual_pct_data(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.UpdateQualPctData', return_value='Success'):
            response = self.app.post('/api/UpdateQualPctData', data={'Year': '2023', 'Company': 'Test Company', 'Cusip': '123456', 'userAction': 'add'})
            self.assertEqual(response.status_code, 200)
            self.assertIn('status', json.loads(response.data))

    @patch('Services.Auth.token_required')
    def test_update_qual_pct_data_failure(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.UpdateQualPctData', return_value='Exists'):
            response = self.app.post('/api/UpdateQualPctData', data={'Year': '2023', 'Company': 'Test Company', 'Cusip': '123456', 'userAction': 'add'})
            self.assertEqual(response.status_code, 200)
            self.assertIn('status', json.loads(response.data))
            self.assertEqual(json.loads(response.data)['status'], 'Exists')

    @patch('Services.Auth.token_required')
    def test_get_qual_pct_data(self, mock_token_required):
        mock_token_required.return_value = True
        with patch('dbops_obj.GetQualPctOverrideData', return_value=[MagicMock(Year='2023', Company='Test Company', Cusip='123456', QualPct=0.5)]):
            response = self.app.get('/api/GetQualPctData')
            self.assertEqual(response.status_code, 200)
            self.assertIn('QualPctData', json.loads(response.data))

if __name__ == '__main__':
    unittest.main()