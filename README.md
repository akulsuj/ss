import unittest
import pandas as pd
import numpy as np
import io
from SADRD_Dataparser import SADRD_Dataparser

class TestSADRD_Dataparser(unittest.TestCase):

    def test_basic_parsing(self):
        data = "col1,col2\n1,2\n3,4"
        parser = SADRD_Dataparser()
        df = parser.parse_data(data)
        self.assertTrue(df is not None)
        self.assertEqual(df.shape, (2, 2))
        self.assertEqual(df.iloc[0, 0], 1)
        self.assertEqual(df.iloc[1, 1], 4)

    def test_different_delimiter(self):
        data = "col1|col2\n1|2\n3|4"
        parser = SADRD_Dataparser(delimiter='|')
        df = parser.parse_data(data)
        self.assertTrue(df is not None)
        self.assertEqual(df.shape, (2, 2))
        self.assertEqual(df.iloc[0, 0], 1)

    def test_skiprows(self):
        data = "skip\ncol1,col2\n1,2\n3,4"
        parser = SADRD_Dataparser(skiprows=1)
        df = parser.parse_data(data)
        self.assertTrue(df is not None)
        self.assertEqual(df.shape, (2, 2))
        self.assertEqual(df.iloc[0, 0], 1)

    def test_header_custom(self):
        data = "1,2\n3,4\n5,6"
        parser = SADRD_Dataparser(header=None)
        df = parser.parse_data(data)
        self.assertTrue(df is not None)
        self.assertEqual(df.shape, (3, 2))
        self.assertEqual(df.columns.tolist(), [0, 1])
        self.assertEqual(df.iloc[0, 0], 1)

    def test_na_values(self):
        data = "col1,col2\n1,NA\n3,4"
        parser = SADRD_Dataparser(na_values=['NA'])
        df = parser.parse_data(data)
        self.assertTrue(df is not None)
        self.assertTrue(pd.isna(df.iloc[0, 1]))

    def test_dtype(self):
        data = "col1,col2\n1,2.0\n3,4.0"
        parser = SADRD_Dataparser(dtype={'col1': int, 'col2': float})
        df = parser.parse_data(data)
        self.assertTrue(df is not None)
        self.assertEqual(df['col1'].dtype, np.int64)
        self.assertEqual(df['col2'].dtype, np.float64)

    def test_file_like_object(self):
        data = "col1,col2\n1,2\n3,4"
        file_like = io.StringIO(data)
        parser = SADRD_Dataparser()
        df = parser.parse_data(file_like)
        self.assertTrue(df is not None)
        self.assertEqual(df.shape, (2, 2))

    def test_parsing_error(self):
        data = "col1,col2\n1,2\n3,4,5"  # Invalid CSV
        parser = SADRD_Dataparser()
        df = parser.parse_data(data)
        self.assertTrue(df is None)

    def test_skiprows_list(self):
        data = "skip1\nskip2\ncol1,col2\n1,2\n3,4"
        parser = SADRD_Dataparser(skiprows=[0,1])
        df = parser.parse_data(data)
        self.assertTrue(df is not None)
        self.assertEqual(df.shape, (2, 2))

    def test_header_list(self):
        data = "h1,h2\n1,2\n3,4\nh3,h4\n5,6"
        parser = SADRD_Dataparser(header=[0,2])
        df = parser.parse_data(data)
        self.assertTrue(df is not None)
        self.assertEqual(df.shape, (2,2))
        self.assertEqual(df.columns.tolist(), ['h1', 'h2'])

if __name__ == '__main__':
    unittest.main(argv=['first-arg-is-ignored'], exit=False)
