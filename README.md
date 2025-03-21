import unittest
from unittest.mock import patch
import subprocess
import sys
from io import StringIO

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
        result = subprocess.run(["python", "SADRD_CLI.py", "abc"], capture_output=True, text=True)
        self.assertEqual(result.stdout.strip(), "ABC")
        self.assertEqual(result.returncode, 0)

    def test_main_with_stdin(self):
        with patch('sys.stdin', StringIO('abc\n')):
            result = subprocess.run(["python", "SADRD_CLI.py"], capture_output=True, text=True)
            self.assertEqual(result.stdout.strip(), "ABC")
            self.assertEqual(result.returncode, 0)

    def test_main_no_argument_no_stdin_tty(self):
        with patch('sys.stdin.isatty', return_value=True):
            result = subprocess.run(["python", "SADRD_CLI.py"], capture_output=True, text=True)
            self.assertIn("Please provide an input string.", result.stdout)
            self.assertEqual(result.returncode, 1)

    def test_main_type_error_handling(self):
        with patch('sys.stdin', StringIO('123')):
            with patch('__main__.process_input', side_effect=TypeError("Test Type Error")):
                result = subprocess.run(["python", "SADRD_CLI.py"], capture_output=True, text=True)
                self.assertIn("Error: Test Type Error", result.stdout)
                self.assertEqual(result.returncode, 1)

    def test_main_general_exception_handling(self):
        with patch('sys.stdin', StringIO('123')):
            with patch('__main__.process_input', side_effect=Exception("Test General Exception")):
                result = subprocess.run(["python", "SADRD_CLI.py"], capture_output=True, text=True)
                self.assertIn("An unexpected error occurred: Test General Exception", result.stdout)
                self.assertEqual(result.returncode, 1)

if __name__ == '__main__':
    unittest.main(argv=['first-arg-is-ignored'], exit=False)
