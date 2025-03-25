import unittest
from unittest.mock import patch
import sys
from io import StringIO
import argparse
import os

def process_input(input_str):
    # ... (your process_input function)

def main():
    # ... (your main function)

if __name__ == "__main__":
    main()

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
        with patch('sys.argv', ['SADRD_CLI.py', 'abc']):
            with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                main()
                self.assertEqual(mock_stdout.getvalue().strip(), "ABC")

    def test_main_with_stdin(self):
        with patch('sys.stdin', StringIO('abc\n')):
            with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                with patch('sys.argv', ['SADRD_CLI.py']):
                    main()
                    self.assertEqual(mock_stdout.getvalue().strip(), "ABC")

    def test_main_no_argument_no_stdin_tty(self):
        with patch('sys.stdin.isatty', return_value=True):
            with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                with patch('sys.exit') as mock_exit:
                    with patch('sys.argv', ['SADRD_CLI.py']):
                        main()
                        self.assertIn("Please provide an input string.", mock_stdout.getvalue())
                        mock_exit.assert_called_once_with(1)

    def test_main_type_error_handling(self):
        with patch('sys.stdin', StringIO('123')):
            with patch('SADRD_CLI.process_input', side_effect=TypeError("Test Type Error")):
                with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                    with patch('sys.exit') as mock_exit:
                        with patch('sys.argv', ['SADRD_CLI.py']):
                            main()
                            self.assertIn("Error: Test Type Error", mock_stdout.getvalue())
                            mock_exit.assert_called_once_with(1)

    def test_main_general_exception_handling(self):
        with patch('sys.stdin', StringIO('123')):
            with patch('SADRD_CLI.process_input', side_effect=Exception("Test General Exception")):
                with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                    with patch('sys.exit') as mock_exit:
                        with patch('sys.argv', ['SADRD_CLI.py']):
                            main()
                            self.assertIn("An unexpected error occurred: Test General Exception", mock_stdout.getvalue())
                            mock_exit.assert_called_once_with(1)
