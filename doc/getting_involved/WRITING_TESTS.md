# How to Write a Test

## Introduction

Tests are an essential element to check if your written components in coala
really do work like they should. Even when you think "I really looked over my
code, no need for tests" you are wrong! Bugs introduced when not writing tests
are often the most horrible ones, they have the characteristic to be
undiscoverable (or only discoverable after dozens of hours of searching).
Try to test as much as possible! The more tests you write the more you can be
sure you did everything correct. Especially if someone else modifies your
component, he can be sure with your tests that he doesn't introduce a bug.

## Actually Writing a Test

So how do you implement a test in coala? First up, tests are placed into the
`bears/tests` (if you want to write a test for a bear) or `coalib/tests` (if
you test a component written for the coalib) directory. They are also written
in python (version 3) and get automatically executed from the global test
script, `run_tests.py`, lying in the coala root folder.
There's only one constraint: The name of the test file has to end with
`Test.py` (for example `MyCustomTest.py`, but not `MyCustomTestSuite.py`).

> **HINT**
>
> Often you don't want to run all available tests. To run your specific one,
> type (in the coala root folder):
>
> ```shell
> ./run_tests.py -t <your-test>
> ```

Coming to the test file structure.
Every test script starts with your system imports, i.e.:

```python
# Imports the python 're' package for regex processing.
import re
# ...
```

Note that you can't import coala components here now.

After that these three lines follow:

```python
import sys
sys.path.insert(0, ".")
import unittest
```

These are necessary imports and setups to make tests working properly in the
coala testing infrastructure. They setup the paths for coala components so you
can now import them as you would do in the written component itself. Don't
change them except you know what you do.

As said before, in the next line your own imports follow. So just import what
you need from coala:

```python
import coalib.your_component.component1
import coalib.your_component.component2
# ...
```

Note here that now system imports do not work, only coala imports.

Then the actual test suite class follows, that contains the tests. Each test
suite is made up of test cases, where the test suite checks the overall
functionality of your component by invoking each test case.

The basic declaration for a test suite class is as follows:

```python
class YourComponentTest(unittest.Test):
    # Your test cases.
```

You should derive your test suite from `unittest.Test` to have access to the
`setUp()` and `tearDown()` functions (covered in section below: **`setUp()` and
`tearDown()`**) and also to the assertion functions.

Now to the test cases: To implement a test case, just declare a class member
function without parameters, starting with `test_`. Easy, isn't it?

```python
class YourComponentTest(unittest.Test):
    # Tests somethin'.
    def test_case1(self):
        pass

    # Doesn't test, this is just a member function, since the function name
    # does not start with 'test_'.
    def not_testing(self):
        pass
```

But how do you actually test if your component is correct? For that purpose
you have asserts. Asserts check whether a condition is fulfilled and pass the
result to the overall test-suite-invoking-instance, that manages all tests in
coala. The result is processed and you get a message if something went wrong in
your test.

So an example test that succeeds would be:

```python
class YourComponentTest(unittest.Test):
    # Tests somethin'.
    def test_case1(self):
        # Does '1' equal '1'? Interestingly it does... mysterious...
        self.assertEqual(1, 1)
        # Hm yeah, True is True.
        self.assertTrue(True)
```
Available assert functions are listed in the section **Assertions** below.

> **NOTE**
>
> Tests in coala are evaluated against their coverage, means how much
> statements will be executed from your component when invoking your test
> cases. You should aim at a branch coverage of 100%.
>
> If some code is untestable, you need to mark your component code with
> `# pragma: no cover`. Important: Provide a reason why your code is
> untestable.
>
> ```python
> # Reason why this function is untestable.
> def untestable_func(): # pragma: no cover
>     # Untestable code.
> ```
>
> Code coverage is measured using python 3.4.

## `setUp()` and `tearDown()`

Often you reuse components or need to make an inital setup for your tests.
For that purpose the function `setUp()` exists. Just declare it inside your
test suite and it is invoked automatically once at test suite startup:

```python
class YourComponentTest(unittest.Test):
    def setUp(self):
        # Your initialization of constants, operating system API calls etc.
```

The opposite from this is the `tearDown()` function. It gets invoked when the
test suite finished running all test cases. Declare it like `setUp()` before:

```python
class YourComponentTest(unittest.Test):
    def tearDown(self):
        # Deinitialization, release calls etc.
```

## Assertions

Here follows a list of all available assertion functions supported when
inheriting from `unittest.Test`:

- `assertEqual(a, b)`

  Checks whether expression `a` equals expression `b`.

- `assertNotEqual(a, b)`

  Checks whether expression `a` **not** equals expression `b`.

- `assertTrue(a)`

  Checks whether expression `a` is True.

- `assertFalse(a)`

  Checks whether expression `a` is False.

- `assertIs(a, b)`

  Checks whether expression `a` `is` `b`.

- `assertIsNot(a, b)`

  Checks whether expression `a` `is not` `b`.

- `assertIsNone(a)`

  Checks whether expression `a` `is None`.

- `assertIsNotNone(a)`

  Checks whether expression `a` `is not None`.

- `assertIn(a, list)`

  Checks whether expression `a` is an element inside `list`.

- `assertNotIn(a, list)`

  Checks whether expression `a` is not an element inside `list`.

- `assertIsInstance(a, type)`

  Checks whether expression `a` is of type `type`.

- `assertNotIsInstance(a, type)`

  Checks whether expression `a` is not of type `type`.

- `assertRaises(error, function, [params...])`

  Checks whether `function` throws the specific `error`. When calling this
  assert it invokes the function with the specified `params`.

If you want more information about the python `unittest`-module, refer to the
[official documentation](https://docs.python.org/3/library/unittest.html) and
for asserts the subsection [assert-methods]
(https://docs.python.org/3/library/unittest.html#assert-methods).

## Template

This section contains a concluding template that you can use as a kickstart for
test-writing.

Put the template under the desired folder inside `coalib/tests` or
`bears/tests`, modify it to let it test your stuff and run from the coala root
folder `./run_tests.py`.

```python
# Import here your needed system components.

import sys
sys.path.insert(0, ".")
import unittest

# Import here your needed coala components.


# Your test unit. The name of this class is displayed in the test evaluation.
class YourTest(unittest.Test):
    def setUp(self):
        # Here you can set up your stuff. For example constant values,
        # initializations etc.
        pass

    def tearDown(self):
        # Here you clean up your stuff initialized in setUp(). For example
        # deleting arrays, call operating system API etc.
        pass

    def test_case1(self):
        # A test method. Put your test code here.
        # You can also add more than one function to test your code. Every
        # function that is not overridden from unittest.Test is executed from
        # the testing python script. Try to group your tests into as many test
        # cases as you can, since it makes your testing code more readable.

        # To test things use the assert functions.
        # Some common ones follow as examples.

        # Check if the two expressions are equal.
        self.assertEqual(1, 1)
        # Check if the two expression not equal.
        self.assertTrue(True)
        # Check whether the function "some_function" raises an Error. The
        # tested function is invoked with the given parameters param1, param2,
        # param3 and so on.
        self.assertRaises(NotImplementedError,
                          some_function,
                          param1,
                          param2,
                          param3)

if __name__ == '__main__':
    unittest.main(verbosity=2)

```

