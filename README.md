import unittest
from unittest.mock import patch
import subprocess
import sys
from io import StringIO
import os

from SADRD_CLI import process_input

class TestSADRD_CLI(unittest.TestCase):

    def test_process_input_empty_string(self):
        self.assertEqual(process_input(""), "")

    def test_process_input_lower_to_upper(self):
        self.assertEqual(process_input("abc"), "ABC")

    def test_process_input_upper_to_lower(self):
        self.assertEqual(process_input("ABC"), "abc")

    def test_process_input_numbers_doubled(self):
        self.assertEqual(process_input("123"), "246")

    def test_process_input_mixed(self):
        self.assertEqual(process_input("aBc12$"), "AbC24$")

    def test_process_input_special_characters(self):
        self.assertEqual(process_input("!@#"), "!@#")

    def test_process_input_type_error(self):
        with self.assertRaises(TypeError):
            process_input(123)

    def test_main_with_argument(self):
        current_dir = os.path.dirname(os.path.abspath(__file__))
        sadrd_cli_path = os.path.join(current_dir, "SADRD_CLI.py")
        result = subprocess.run(["python", sadrd_cli_path, "abc"], capture_output=True, text=True)
        self.assertEqual(result.stdout.strip(), "ABC")
        self.assertEqual(result.returncode, 0)

    def test_main_with_stdin(self):
        current_dir = os.path.dirname(os.path.abspath(__file__))
        sadrd_cli_path = os.path.join(current_dir, "SADRD_CLI.py")
        with patch('sys.stdin', StringIO('abc\n')):
            result = subprocess.run(["python", sadrd_cli_path], capture_output=True, text=True)
            self.assertEqual(result.stdout.strip(), "ABC")
            self.assertEqual(result.returncode, 0)

    def test_main_no_argument_no_stdin_tty(self):
        current_dir = os.path.dirname(os.path.abspath(__file__))
        sadrd_cli_path = os.path.join(current_dir, "SADRD_CLI.py")
        with patch('sys.stdin.isatty', return_value=True):
            result = subprocess.run(["python", sadrd_cli_path], capture_output=True, text=True)
            self.assertIn("Please provide an input string.", result.stdout)
            self.assertEqual(result.returncode, 1)

    def test_main_type_error_handling(self):
        current_dir = os.path.dirname(os.path.abspath(__file__))
        sadrd_cli_path = os.path.join(current_dir, "SADRD_CLI.py")
        with patch('sys.stdin', StringIO('123')):
            with patch('SADRD_CLI.process_input', side_effect=TypeError("Test Type Error")):
                result = subprocess.run(["python", sadrd_cli_path], capture_output=True, text=True)
                self.assertIn("Error: Test Type Error", result.stdout)
                self.assertEqual(result.returncode, 1)

    def test_main_general_exception_handling(self):
        current_dir = os.path.dirname(os.path.abspath(__file__))
        sadrd_cli_path = os.path.join(current_dir, "SADRD_CLI.py")
        with patch('sys.stdin', StringIO('123')):
            with patch('SADRD_CLI.process_input', side_effect=Exception("Test General Exception")):
                result = subprocess.run(["python", sadrd_cli_path], capture_output=True, text=True)
                self.assertIn("An unexpected error occurred: Test General Exception", result.stdout)
                self.assertEqual(result.returncode, 1)
