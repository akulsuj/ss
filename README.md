import unittest
from unittest.mock import patch, mock_open
import os
from datetime import datetime
from pathlib import Path
import sys

sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(__file__), '../Services')))
from Services.logoperations import currentDateTimeWithTimezone, insertServerEventLog

class TestLoggingFunctions(unittest.TestCase):

    def test_currentDateTimeWithTimezone(self):
        result = currentDateTimeWithTimezone()
        try:
            datetime.strptime(result, '%Y-%m-%dT%H:%M:%S.%f%z')
        except ValueError:
            self.fail("currentDateTimeWithTimezone() returned an invalid datetime format")

        self.assertIn(result[23:24], ['+', '-'])
        self.assertEqual(result[26], ':')

    @patch('builtins.open', new_callable=mock_open)
    @patch('Services.logoperations.currentDateTimeWithTimezone', return_value='2021-12-05T08:33:53.165-05:00')
    def test_insertServerEventLog(self, mock_current_time, mock_open):
        environment = 'local'
        serverLogFilesFolder = '/fake/path'
        eventType = 'Login'
        eventCriticality = 'INFO'
        networkId = 'user123'
        apiEndpoint = 'http://example.com'
        apiFunction = 'login'
        activityId = 'activity123'
        userIp = '192.168.1.1'
        appServerIP = '10.0.0.1'
        logStandard = 'RFC5424'
        expected_path = str(Path.cwd().parent.joinpath('Logs', 'US_Tax_SADRD.log'))

        insertServerEventLog(environment, serverLogFilesFolder, eventType, eventCriticality, networkId,
                              apiEndpoint, apiFunction, activityId, userIp, appServerIP, logStandard)

        mock_open.assert_called_once()
        actual_path = mock_open.call_args[0][0]
        self.assertEqual(os.path.normpath(actual_path), os.path.normpath(expected_path))

        handle = mock_open()
        handle.write.assert_called_once()
        written_log = handle.write.call_args[0][0]

        self.assertIn('"LogStandard": "RFC5424"', written_log)
        self.assertIn('"EventTime": "2021-12-05T08:33:53.165-05:00"', written_log)
        self.assertIn('"LoginResult": "Login"', written_log)
        self.assertIn('"LogLevel": "INFO"', written_log)
        self.assertIn('"Username": "user123"', written_log)
        self.assertIn('"AppURL": "http://example.com/api/login"', written_log)
        self.assertIn('"ActivityID": "activity123"', written_log)
        self.assertIn('"SourceIP": "192.168.1.1"', written_log)
        self.assertIn('"AppServerIP": "10.0.0.1"', written_log)

    @patch('builtins.open', new_callable=mock_open)
    @patch('Services.logoperations.currentDateTimeWithTimezone', return_value='2021-12-05T08:33:53.165-05:00')
    def test_insertServerEventLog_with_different_environment(self, mock_current_time, mock_open):
        environment = 'production'
        serverLogFilesFolder = '/fake/path'
        eventType = 'Logout'
        eventCriticality = 'ERROR'
        networkId = 'user456'
        apiEndpoint = 'http://example.com'
        apiFunction = 'logout'
        activityId = 'activity456'
        userIp = '192.168.1.2'
        appServerIP = '10.0.0.2'
        logStandard = 'RFC5424'
        expected_path = os.path.join(serverLogFilesFolder, 'US_Tax_SADRD.log')

        insertServerEventLog(environment, serverLogFilesFolder, eventType, eventCriticality, networkId,
                              apiEndpoint, apiFunction, activityId, userIp, appServerIP, logStandard)

        mock_open.assert_called_once()
        actual_path = mock_open.call_args[0][0]
        self.assertEqual(os.path.normpath(actual_path), os.path.normpath(expected_path))

        handle = mock_open()
        handle.write.assert_called_once()
        written_log = handle.write.call_args[0][0]

        self.assertIn('"LogStandard": "RFC5424"', written_log)
        self.assertIn('"EventTime": "2021-12-05T08:33:53.165-05:00"', written_log)
        self.assertIn('"LoginResult": "Logout"', written_log)
        self.assertIn('"LogLevel": "ERROR"', written_log)
        self.assertIn('"Username": "user456"', written_log)
        self.assertIn('"AppURL": "http://example.com/api/logout"', written_log)
        self.assertIn('"ActivityID": "activity456"', written_log)
        self.assertIn('"SourceIP": "192.168.1.2"', written_log)
        self.assertIn('"AppServerIP": "10.0.0.2"', written_log)
