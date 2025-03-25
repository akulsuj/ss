import unittest
from unittest.mock import patch, MagicMock
from flask import Flask, request
from io import BytesIO
import json
import datetime
from pathlib import Path
import urllib
import pandas as pd

import APIHome
import globalvars as gvar
from db import db
import Services.dboperations as dbops
import Services.Auth as auth
from Entities.Customentities import ApihomeResp

class TestAPIHome(unittest.TestCase):

    def setUp(self):
        self.app = APIHome.mainapp
        self.client = self.app.test_client()
        self.app.config['TESTING'] = True
        self.app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///:memory:'
        self.app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
        self.app.config['PROPAGATE_EXCEPTIONS'] = True
        self.app.config['CORS_ORIGINS'] = '{"test": ["*"]}'
        self.app.config['LOGLEVEL'] = 'DEBUG'
        self.app.config['SADRD_SERVER_FOLDER'] = '{"serverFolderPath": "test_server_folder"}'
        self.app.config['SERVER_LOGFILES_FOLDER'] = 'test_logs'
        self.app.config['API_ENDPOINT'] = '/api'
        self.app.config['APP_SERVER_IP_ADDRESS'] = '127.0.0.1'
        self.app.config['DRIVER'] = '{ODBC Driver 17 for SQL Server}'
        self.app.config['SADRD_DATABASE_SERVER'] = 'test_server'
        self.app.config['SADRD_DATABASE_NAME'] = 'test_db'
        self.app.config['CONNECTION_AUTH_STRING'] = ';Trusted_Connection=yes;'
        self.app.config['SQLALCHEMYODBC'] = 'mssql+pyodbc:///?odbc_connect=%s'

        with self.app.app_context():
            db.init_app(self.app)
            db.create_all()

        gvar.init()
        gvar.gconfig = self.app.config
        gvar.sqlconfig = urllib.parse.quote_plus("DRIVER="+gvar.gconfig["DRIVER"] + ";" + "SERVER=" + gvar.gconfig["SADRD_DATABASE_SERVER"] + ";" + "DATABASE=" + gvar.gconfig["SADRD_DATABASE_NAME"] + gvar.gconfig["CONNECTION_AUTH_STRING"])
        self.dbops_obj = dbops.dboperations()
        gvar.sadrdUsersList = self.dbops_obj.GetAllUsers()
        gvar.sadrd_ErrMessages = self.dbops_obj.SADRD_Sys_Message()

    def tearDown(self):
        with self.app.app_context():
            db.session.remove()
            db.drop_all()

    @patch('Services.Auth.token_required', MagicMock(side_effect=lambda f: f))
    @patch('Services.Auth.GetLoggedInUser', MagicMock(return_value='testuser'))
    @patch('Services.dboperations.dboperations.GetAllUsers', MagicMock(return_value=[MagicMock(NetworkId='testuser', Name='Test User', RoleId=1, isActive=True, Email='test@example.com')]))
    @patch('Services.dboperations.dboperations.SadrdSysSettings', MagicMock(return_value=[MagicMock(settingName='test', settingValue='test')]))
    @patch('APIHome.GetAllRoles', MagicMock(return_value=MagicMock(json={'RolesData': [{'RoleID': 1, 'Type': 'Admin'}]})))
    @patch('Services.logoperations.insertServerEventLog', MagicMock())
    def test_authenticate_user_success(self):
        response = APIHome.AuthenticateUser()
        self.assertEqual(response, {'authenticated': True, 'NetworkId': 'testuser', 'Name': 'Test User', 'RoleId': 1, 'IsActive': True, 'Email': 'test@example.com', 'Role': 'Admin'})

    @patch('Services.Auth.token_required', MagicMock(side_effect=lambda f: f))
    @patch('Services.Auth.GetLoggedInUser', MagicMock(return_value='testuser'))
    @patch('Services.dboperations.dboperations.GetAllUsers', MagicMock(return_value=[]))
    @patch('Services.logoperations.insertServerEventLog', MagicMock())
    def test_authenticate_user_no_users(self):
        response = APIHome.AuthenticateUser()
        self.assertEqual(response, {'authenticated': False})

    @patch('Services.Auth.token_required', MagicMock(side_effect=lambda f: f))
    @patch('Services.Auth.GetLoggedInUser', MagicMock(return_value='testuser'))
    @patch('Services.dboperations.dboperations.GetAllUsers', MagicMock(return_value=[MagicMock(NetworkId='testuser2', Name='Test User', RoleId=1, isActive=True, Email='test@example.com')]))
    @patch('Services.logoperations.insertServerEventLog', MagicMock())
    def test_authenticate_user_no_match(self):
        response = APIHome.AuthenticateUser()
        self.assertEqual(response, {'authenticated': False})

    @patch('Services.Auth.token_required', MagicMock(side_effect=lambda f: f))
    @patch('Services.Auth.GetLoggedInUser', MagicMock(return_value='testuser'))
    @patch('Services.dboperations.dboperations.GetAllUsers', MagicMock(side_effect=Exception('test')))
    @patch('Services.logoperations.insertServerEventLog', MagicMock())
    def test_authenticate_user_exception(self):
        response = APIHome.AuthenticateUser()
        self.assertEqual(response, None)

    @patch('Services.Auth.token_required', MagicMock(side_effect=lambda f: f))
    @patch('Services.dboperations.dboperations.SadrdSysSettings', MagicMock(return_value=[MagicMock(settingName='ImportType', settingValue='test', Description='test desc')]))
    def test_get_import_types(self):
        response = self.client.get('/api/GetImportTypes')
        self.assertEqual(response.json, {'ImportTypesData': [{'ImportId': 1, 'ImportType': 'test', 'Description': 'test desc'}]})

    @patch('Services.Auth.token_required', MagicMock(side_effect=lambda f: f))
    @patch('Services.dboperations.dboperations.SadrdSysSettings', MagicMock(side_effect=Exception('test')))
    def test_get_import_types_exception(self):
        response = self.client.get('/api/GetImportTypes')
        self.assertEqual(response.json, {'ImportTypesData': []})

    @patch('Services.Auth.token_required', MagicMock(side_effect=lambda f: f))
    @patch('Services.parentparser.parentparser', MagicMock(return_value=ApihomeResp(status='Success', message='test message')))
    @patch('Services.dboperations.dboperations.SadrdSysSettings', MagicMock(return_value=[MagicMock(settingName='ServerFolderPath', settingValue='test_server_folder')]))
    @patch('Services.logoperations.insertServerEventLog', MagicMock())
    def test_import_data_success(self):
        response = self.client.post('/api/ImportData', data={'year': '2023', 'importType': 'test'})
        self.assertEqual(response.json, {'status': 'Success', 'message': 'test message'})

    @patch('Services.Auth.token_required', MagicMock(side_effect=lambda f: f))
    @patch('Services.parentparser.parentparser', MagicMock(return_value=ApihomeResp(status='Failure', message='test message')))
    @patch('Services.dboperations.dboperations.SadrdSysSettings', MagicMock(return_value=[MagicMock(settingName='ServerFolderPath', settingValue='test_server_folder')]))
    @patch('Services.logoperations.insertServerEventLog', MagicMock())
    def test_import_data_failure(self):
        response =
