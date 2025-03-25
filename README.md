import unittest
from unittest.mock import patch
import subprocess
import sys
from io import StringIO
import argparse

def process_input(input_str):
    """Processes the input string and returns a modified string."""
    if not isinstance(input_str, str):
        raise TypeError("Input must be a string.")

    if not input_str:
        return ""

    modified_str = ""
    for char in input_str:
        if 'a' <= char <= 'z':
            modified_str += char.upper()
        elif 'A' <= char <= 'Z':
            modified_str += char.lower()
        elif '0' <= char <= '9':
            modified_str += str(int(char) * 2)
        else:
            modified_str += char

    return modified_str

def main():
    """Main function to parse arguments and process input."""
    parser = argparse.ArgumentParser(description="Process input string.")
    parser.add_argument("input_string", nargs="?", default=None, help="Input string to process.")
    args = parser.parse_args()

    if args.input_string is None:
        if sys.stdin.isatty():
            print("Please provide an input string.")
            sys.exit(1)
        else:
            input_str = sys.stdin.read().strip()
    else:
        input_str = args.input_string

    try:
        result = process_input(input_str)
        print(result)
    except TypeError as e:
        print(f"Error: {e}")
        sys.exit(1)
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
        sys.exit(1)

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
                main()
                self.assertEqual(mock_stdout.getvalue().strip(), "ABC")

    def test_main_no_argument_no_stdin_tty(self):
        with patch('sys.stdin.isatty', return_value=True):
            with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                with patch('sys.exit') as mock_exit:
                    main()
                    self.assertIn("Please provide an input string.", mock_stdout.getvalue())
                    mock_exit.assert_called_once_with(1)

    def test_main_type_error_handling(self):
        with patch('sys.stdin', StringIO('123')):
            with patch('__main__.process_input', side_effect=TypeError("Test Type Error")):
                with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                    with patch('sys.exit') as mock_exit:
                        main()
                        self.assertIn("Error: Test Type Error", mock_stdout.getvalue())
                        mock_exit.assert_called_once_with(1)

    def test_main_general_exception_handling(self):
        with patch('sys.stdin', StringIO('123')):
            with patch('__main__.process_input', side_effect=Exception("Test General Exception")):
                with patch('sys.stdout', new_callable=StringIO) as mock_stdout:
                    with patch('sys.exit') as mock_exit:
                        main()
                        self.assertIn("An unexpected error occurred: Test General Exception", mock_stdout.getvalue())
                        mock_exit.assert_called_once_with(1)
