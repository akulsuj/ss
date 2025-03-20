pytest --cov . test/ --cov-report html
===================================================== test session starts =====================================================
platform win32 -- Python 3.9.13, pytest-7.2.0, pluggy-1.5.0
rootdir: C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API
plugins: Flask-Dance-3.2.0, cov-4.0.0
collected 83 items

test\test_APIHome.py .........                                                                                           [ 10%]
test\test_config.py .....................                                                                                [ 36%]
test\test_db.py .                                                                                                        [ 37%]
test\test_globalvars.py .                                                                                                [ 38%] 
test\Entities\test_Customentities.py ....                                                                                [ 43%]
test\Entities\test_dbormschemas.py .........                                                                             [ 54%]
test\Services\test_APIResponse.py .......                                                                                [ 62%]
test\Services\test_Auth.py ........                                                                                      [ 72%]
test\Services\test_CustomException.py ..                                                                                 [ 74%] 
test\Services\test_dboperations.py ...........                                                                           [ 87%]
test\Services\test_fileoperations.py ....                                                                                [ 92%]
test\Services\test_logoperations.py ...                                                                                  [ 96%]
test\Services\test_parentparser.py .FF                                                                                   [100%]

========================================================== FAILURES =========================================================== 
__________________________________ TestParentParser.test_parentparser_generate_sadrd_report ___________________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_generate_sadrd_report>
mock_load_vpa = <MagicMock name='LoadVPADataToSADRD' id='2458361339856'>
mock_load_cusip = <MagicMock name='Load_FundCusipDetails' id='2458361372048'>
mock_execute_view = <MagicMock name='executeView_GetDetails' id='2458361396144'>
mock_dbops_constructor = <MagicMock name='dboperations' id='2458361412384'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.sdp.executeView_GetDetails')
    @patch('Services.parentparser.sdp.Load_FundCusipDetails')
    @patch('Services.parentparser.sdp.LoadVPADataToSADRD')
    def test_parentparser_generate_sadrd_report(self, mock_load_vpa, mock_load_cusip, mock_execute_view, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_dbops_instance.SadrdSysSettings.return_value = [
            MagicMock(settingName="IsRefreshUVAndVPA", settingValue="Y"),
            MagicMock(settingName="Valid_Company", settingValue="test_company"),
            MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")
        ]
        mock_execute_view.return_value = pd.DataFrame()
>       parentparser({"action1": ["file1.txt"]}, "/input/dir/", "generateSADRDReport", 2025)

test\Services\test_parentparser.py:78:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
Services\parentparser.py:224: in parentparser
    raise e
Services\parentparser.py:160: in parentparser
    dfRpsFundCusipDetails.insert(4, 'Source', 'UV')
venv\lib\site-packages\pandas\core\frame.py:3763: in insert
    self._mgr.insert(loc, column, value, allow_duplicates=allow_duplicates)
venv\lib\site-packages\pandas\core\internals\managers.py:1220: in insert
    self._blklocs = np.insert(self._blklocs, loc, 0)
<__array_function__ internals>:180: in insert
    ???
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

arr = array([], dtype=int64), obj = 4, values = 0, axis = 0

    @array_function_dispatch(_insert_dispatcher)
    def insert(arr, obj, values, axis=None):
        """
        Insert values along the given axis before the given indices.

        Parameters
        ----------
        arr : array_like
            Input array.
        obj : int, slice or sequence of ints
            Object that defines the index or indices before which `values` is
            inserted.

            .. versionadded:: 1.8.0

            Support for multiple insertions when `obj` is a single scalar or a
            sequence with one element (similar to calling insert multiple
            times).
        values : array_like
            Values to insert into `arr`. If the type of `values` is different
            from that of `arr`, `values` is converted to the type of `arr`.
            `values` should be shaped so that ``arr[...,obj,...] = values``
            is legal.
        axis : int, optional
            Axis along which to insert `values`.  If `axis` is None then `arr`
            is flattened first.

        Returns
        -------
        out : ndarray
            A copy of `arr` with `values` inserted.  Note that `insert`
            does not occur in-place: a new array is returned. If
            `axis` is None, `out` is a flattened array.

        See Also
        --------
        append : Append elements at the end of an array.
        concatenate : Join a sequence of arrays along an existing axis.
        delete : Delete elements from an array.

        Notes
        -----
        Note that for higher dimensional inserts `obj=0` behaves very different
        from `obj=[0]` just like `arr[:,0,:] = values` is different from
        `arr[:,[0],:] = values`.

        Examples
        --------
        >>> a = np.array([[1, 1], [2, 2], [3, 3]])
        >>> a
        array([[1, 1],
               [2, 2],
               [3, 3]])
        >>> np.insert(a, 1, 5)
        array([1, 5, 1, ..., 2, 3, 3])
        >>> np.insert(a, 1, 5, axis=1)
        array([[1, 5, 1],
               [2, 5, 2],
               [3, 5, 3]])

        Difference between sequence and scalars:

        >>> np.insert(a, [1], [[1],[2],[3]], axis=1)
        array([[1, 1, 1],
               [2, 2, 2],
               [3, 3, 3]])
        >>> np.array_equal(np.insert(a, 1, [1, 2, 3], axis=1),
        ...                np.insert(a, [1], [[1],[2],[3]], axis=1))
        True

        >>> b = a.flatten()
        >>> b
        array([1, 1, 2, 2, 3, 3])
        >>> np.insert(b, [2, 2], [5, 6])
        array([1, 1, 5, ..., 2, 3, 3])

        >>> np.insert(b, slice(2, 4), [5, 6])
        array([1, 1, 5, ..., 2, 3, 3])

        >>> np.insert(b, [2, 2], [7.13, False]) # type casting
        array([1, 1, 7, ..., 2, 3, 3])

        >>> x = np.arange(8).reshape(2, 4)
        >>> idx = (1, 3)
        >>> np.insert(x, idx, 999, axis=1)
        array([[  0, 999,   1,   2, 999,   3],
               [  4, 999,   5,   6, 999,   7]])

        """
        wrap = None
        if type(arr) is not ndarray:
            try:
                wrap = arr.__array_wrap__
            except AttributeError:
                pass

        arr = asarray(arr)
        ndim = arr.ndim
        arrorder = 'F' if arr.flags.fnc else 'C'
        if axis is None:
            if ndim != 1:
                arr = arr.ravel()
            # needed for np.matrix, which is still not 1d after being ravelled
            ndim = arr.ndim
            axis = ndim - 1
        else:
            axis = normalize_axis_index(axis, ndim)
        slobj = [slice(None)]*ndim
        N = arr.shape[axis]
        newshape = list(arr.shape)

        if isinstance(obj, slice):
            # turn it into a range object
            indices = arange(*obj.indices(N), dtype=intp)
        else:
            # need to copy obj, because indices will be changed in-place
            indices = np.array(obj)
            if indices.dtype == bool:
                # See also delete
                # 2012-10-11, NumPy 1.8
                warnings.warn(
                    "in the future insert will treat boolean arrays and "
                    "array-likes as a boolean index instead of casting it to "
                    "integer", FutureWarning, stacklevel=3)
                indices = indices.astype(intp)
                # Code after warning period:
                #if obj.ndim != 1:
                #    raise ValueError('boolean array argument obj to insert '
                #                     'must be one dimensional')
                #indices = np.flatnonzero(obj)
            elif indices.ndim > 1:
                raise ValueError(
                    "index array argument obj to insert must be one dimensional "
                    "or scalar")
        if indices.size == 1:
            index = indices.item()
            if index < -N or index > N:
>               raise IndexError(f"index {obj} is out of bounds for axis {axis} "
                                 f"with size {N}")
E               IndexError: index 4 is out of bounds for axis 0 with size 0

venv\lib\site-packages\numpy\lib\function_base.py:5280: IndexError
---------------------------------------------------- Captured stderr call ----------------------------------------------------- 
root        : ERROR    Exception occurred in parentparser() method :: index 4 is out of bounds for axis 0 with size 0
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\parentparser.py", line 160, in parentparser
    dfRpsFundCusipDetails.insert(4, 'Source', 'UV')
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\core\frame.py", line 3763, in insert
    self._mgr.insert(loc, column, value, allow_duplicates=allow_duplicates)
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\core\internals\managers.py", line 1220, in insert
    self._blklocs = np.insert(self._blklocs, loc, 0)
  File "<__array_function__ internals>", line 180, in insert
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\numpy\lib\function_base.py", line 5280, in insert   
    raise IndexError(f"index {obj} is out of bounds for axis {axis} "
IndexError: index 4 is out of bounds for axis 0 with size 0
------------------------------------------------------ Captured log call ------------------------------------------------------ 
ERROR    root:parentparser.py:220 Exception occurred in parentparser() method :: index 4 is out of bounds for axis 0 with size 0
Traceback (most recent call last):
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\Services\parentparser.py", line 160, in parentparser
    dfRpsFundCusipDetails.insert(4, 'Source', 'UV')
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\core\frame.py", line 3763, in insert
    self._mgr.insert(loc, column, value, allow_duplicates=allow_duplicates)
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\pandas\core\internals\managers.py", line 1220, in insert
    self._blklocs = np.insert(self._blklocs, loc, 0)
  File "<__array_function__ internals>", line 180, in insert
  File "C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\numpy\lib\function_base.py", line 5280, in insert   
    raise IndexError(f"index {obj} is out of bounds for axis {axis} "
IndexError: index 4 is out of bounds for axis 0 with size 0
_____________________________ TestParentParser.test_parentparser_generate_sadrd_report_no_refresh _____________________________ 

self = <test.Services.test_parentparser.TestParentParser testMethod=test_parentparser_generate_sadrd_report_no_refresh>
mock_execute_view = <MagicMock name='executeView_GetDetails' id='2458361072320'>
mock_dbops_constructor = <MagicMock name='dboperations' id='2458360933296'>

    @patch('Services.parentparser.dbops.dboperations')
    @patch('Services.parentparser.sdp.executeView_GetDetails')
    def test_parentparser_generate_sadrd_report_no_refresh(self, mock_execute_view, mock_dbops_constructor):
        from Services.parentparser import parentparser
        mock_dbops_instance = MagicMock()
        mock_dbops_constructor.return_value = mock_dbops_instance
        mock_dbops_instance.SadrdSysSettings.return_value = [
            MagicMock(settingName="IsRefreshUVAndVPA", settingValue="N"),
            MagicMock(settingName="Valid_Company", settingValue="test_company"),
            MagicMock(settingName="IncludePartType_Funds", settingValue="test_fund")
        ]
        parentparser({"action1": ["file1.txt"]}, "/input/dir/", "generateSADRDReport", 2025)
>       mock_execute_view.assert_called()

test\Services\test_parentparser.py:95:
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 

self = <MagicMock name='executeView_GetDetails' id='2458361072320'>

    def assert_called(self):
        """assert that the mock was called at least once
        """
        if self.call_count == 0:
            msg = ("Expected '%s' to have been called." %
                   (self._mock_name or 'mock'))
>           raise AssertionError(msg)
E           AssertionError: Expected 'executeView_GetDetails' to have been called.

C:\Program Files\Python39\lib\unittest\mock.py:876: AssertionError
====================================================== warnings summary ======================================================= 
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

venv\lib\site-packages\flask_sqlalchemy\__init__.py:14
venv\lib\site-packages\flask_sqlalchemy\__init__.py:14
  C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API\venv\lib\site-packages\flask_sqlalchemy\__init__.py:14: DeprecationWarning: '_app_ctx_stack' is deprecated and will be removed in Flask 2.3.
    from flask import _app_ctx_stack, abort, current_app, request

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html

---------- coverage: platform win32, python 3.9.13-final-0 -----------
Coverage HTML written to dir htmlcov

=================================================== short test summary info ===================================================
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_generate_sadrd_report - IndexError: index 4 is out of bounds for axis 0 with size 0
FAILED test/Services/test_parentparser.py::TestParentParser::test_parentparser_generate_sadrd_report_no_refresh - AssertionError: Expected 'executeView_GetDetails' to have been called.
========================================== 2 failed, 81 passed, 10 warnings in 4.23s ========================================== 
PS C:\Sujith\Projects\SADRD\FinanceIT_SADRD\API> 
