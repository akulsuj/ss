import unittest
from unittest.mock import MagicMock, patch
from flask import Flask, jsonify
import globalvars as gvar
import Services.Auth as auth

class Test_Auth(unittest.TestCase):

    def setUp(self):
        self.app = Flask(__name__)
        self.client = self.app.test_client()
        gvar.sadrdUsersList = []
        gvar.ISAUTHORIZED = False

    @patch('Services.Auth.DecryptToken')
    @patch('Services.Auth.GetLoggedInUser')
    @patch('Services.dboperations.dboperations.insert_actionLog')
    def test_token_required_success(self, mock_insert_actionLog, mock_get_logged_in_user, mock_decrypt_token):
        gvar.sadrdUsersList = [MagicMock(NetworkId='test_user')]
        mock_decrypt_token.return_value = {'name': 'Test User', 'ipaddr': '127.0.0.1'}
        mock_get_logged_in_user.return_value = 'test_user'

        @auth.token_required
        def test_route():
            return jsonify({'message': 'Success'})

        with self.app.test_request_context(headers={'Authorization': 'Bearer valid_token'}):
            response = test_route()
            self.assertEqual(response.status_code, 200)
            self.assertEqual(response.json, {'message': 'Success'})
            self.assertTrue(gvar.ISAUTHORIZED)
            mock_insert_actionLog.assert_not_called()

    @patch('Services.Auth.DecryptToken')
    @patch('Services.Auth.GetLoggedInUser')
    def test_token_required_unauthorized_user_not_in_list(self, mock_get_logged_in_user, mock_decrypt_token):
        gvar.sadrdUsersList = [MagicMock(NetworkId='other_user')]
        mock_decrypt_token.return_value = {'name': 'Test User', 'ipaddr': '127.0.0.1'}
        mock_get_logged_in_user.return_value = 'test_user'

        @auth.token_required
        def test_route():
            return jsonify({'message': 'Success'})

        with self.app.test_request_context(headers={'Authorization': 'Bearer valid_token'}):
            response = test_route()
            self.assertEqual(response.status_code, 200)
            self.assertEqual(response.json, {'apirespmsg': 'Unauthorized Access'})
            self.assertFalse(gvar.ISAUTHORIZED)

    @patch('Services.Auth.DecryptToken')
    @patch('Services.Auth.GetLoggedInUser')
    def test_token_required_unauthorized_empty_list(self, mock_get_logged_in_user, mock_decrypt_token):
        gvar.sadrdUsersList = []
        mock_decrypt_token.return_value = {'name': 'Test User', 'ipaddr': '127.0.0.1'}
        mock_get_logged_in_user.return_value = 'test_user'

        @auth.token_required
        def test_route():
            return jsonify({'message': 'Success'})

        with self.app.test_request_context(headers={'Authorization': 'Bearer valid_token'}):
            response = test_route()
            self.assertEqual(response.status_code, 200)
            self.assertEqual(response.json, {'apirespmsg': 'Unauthorized Access'})
            self.assertFalse(gvar.ISAUTHORIZED)

    def test_token_required_no_auth_header(self):
        @auth.token_required
        def test_route():
            return jsonify({'message': 'Success'})

        with self.app.test_request_context():
            response = test_route()
            self.assertEqual(response.status_code, 200)
            self.assertEqual(response.json, {'apirespmsg': 'Authorization Token is missing!'})
            self.assertFalse(gvar.ISAUTHORIZED)

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
            mock_insert_actionLog.assert_called_once()
