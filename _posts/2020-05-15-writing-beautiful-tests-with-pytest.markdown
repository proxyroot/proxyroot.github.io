---
layout: post
title:  Writing  beautiful tests with pytest
description: Let's walk through a bunch of use cases that i've learnt over a period of time for writing tests.
date:   2020-05-15 15:11:00 +0500
categories: Python pytest moto boto3 s3 aws freezegun datetime
---

Let's walk through a bunch of use cases that i've learnt over a period of time for writing tests.

### Introduction:

As a developer when i started writing code i didn't realized the importance of a test case. The first time i heard test driven development, i wanted to see what that is and i found out that test driven development is a good way of writing code rather than just writing code and expect it to work. Let's walk through a bunch of use cases that i've learnt over a period of time.

### Initial setup

```bash
mkdir testing && cd testing && poetry init
```

`Note: Istructions on poetry setup is easy. Find out more in my article` ["Setup Django with poetry"](https://proxyroot.com/django-poetry/)

### Install packages:

- [pytest](https://github.com/pytest-dev/pytest)
- [freezegun](https://github.com/spulec/freezegun)
- [moto](https://github.com/spulec/moto)
- [boto3](https://github.com/boto/boto3)

```bash
$ poetry add <package-name>
``````

### Shenanigans of datetime

Lets write a quick function that returns a datetime object as below to functions.py file


```python
import datetime


def five_days_from_now():
    return datetime.datetime.now() + datetime.timedelta(days=5)
```

A simple test for this would be

```python
import datetime

from functions import five_days_from_now


def test_five_days_from_now():
    assert five_days_from_now() == datetime.datetime.now() + datetime.timedelta(days=5)
```

Lets run tests
```bash
$ poetry run pytest

============================= test session starts ==============================
platform darwin -- Python 3.6.8rc1, pytest-5.4.2, py-1.8.1, pluggy-0.13.1
collected 1 item                                                               

test_functions.py F                                                      [100%]

=================================== FAILURES ===================================
___________________________ test_five_days_from_now ____________________________

    def test_five_days_from_now():
>       assert five_days_from_now() == datetime.datetime.now()
E       AssertionError: assert datetime.datetime(2020, 5, 15, 16, 26, 42, 528109) == datetime.datetime(2020, 5, 15, 16, 26, 42, 528114)
E        +  where datetime.datetime(2020, 5, 15, 16, 26, 42, 528109) = five_days_from_now()
E        +  and   datetime.datetime(2020, 5, 15, 16, 26, 42, 528114) = <built-in method now of type object at 0x104d4d680>()
E        +    where <built-in method now of type object at 0x104d4d680> = <class 'datetime.datetime'>.now
E        +      where <class 'datetime.datetime'> = datetime.datetime

test_functions.py:7: AssertionError
=========================== short test summary info ============================
FAILED test_functions.py::test_five_days_from_now - AssertionError: assert da...
============================== 1 failed in 0.05s ===============================
```

Well obviously that was gonna fail because there is a difference in the time when the test calling datetime.now() and function calling datetime.now(). You might see that this is the case with some of your tests and the first thing that might come to your mind is that `mock` datetime. Let's do this in a better way!!!



```python
import datetime

import freezegun

from functions import five_days_from_now


@freezegun.freeze_time("2020-05-15")
def test_five_days_from_now():
    assert five_days_from_now() == datetime.datetime.now() + datetime.timedelta(days=5)
```

Let's run the test again

```bash
$ poetry run pytest
============================== test session starts =============================
platform darwin -- Python 3.6.8rc1, pytest-5.4.2, py-1.8.1, pluggy-0.13.1
collected 1 item

test_functions.py .                                                       [100%]

=============================== 1 passed in 0.29s ==============================

$
```

Hooray!! As long as the freezegun is used as decorator the datetime will always give `2020-05-15` as the date.

It is possible that sometimes we might not wana mock everytime. We can use with context to make this happen.

Let's edit our test case again

```python
import datetime

import freezegun

from functions import five_days_from_now


def test_five_days_from_now():
    # Freezing time
    with freezegun.freeze_time("2012-01-14"):
        assert five_days_from_now() == datetime.datetime.now() + datetime.timedelta(days=5)

    # call five_days_from_now without freeze
    assert five_days_from_now() != datetime.datetime.now() + datetime.timedelta(days=5)
```

Let's run our test again

```bash
$ poetry run pytest
============================== test session starts =============================
platform darwin -- Python 3.6.8rc1, pytest-5.4.2, py-1.8.1, pluggy-0.13.1
collected 1 item

test_functions.py .                                                       [100%]

=============================== 1 passed in 0.11s ==============================

$
```

Freezegun will only mock the datetime with in the context and its lifted once it comes out of the context.

Enough of the Shenanigans lets talk moto.

### Test with moto

Lets add a function that creates a s3 file in functions.py

```python
import boto3


def create_file(path, content):
    """
    Creates a file in s3 bucket given a path to the file and content to write
    :path: <str>
    :content: <str>
    """
    client = boto3.client("s3", region_name="us-east-1")
    client.put_object(
        Body=content.encode("utf-8"), Bucket="bucket.proxyroot.com", Key=path
    )
```


Let's create a fixture with pytest for s3 in test_functions.py
```python
import boto3

from moto import mock_s3


@pytest.fixture(scope="function")
def s3_client():
    with mock_s3():
        conn = boto3.resource("s3", region_name="us-east-1")
        # create a bucket
        conn.create_bucket(Bucket="bucket.proxyroot.com")

        client = boto3.client("s3", region_name="us-east-1")

        yield client
```

Let's create a test in test_functions.py

```python
from functions import create_file


def test_create_file(s3_client):
    # Create test.txt file
    create_file("tests/test.txt", "hello world")

    # get the object contents
    object = s3_client.get_object(Bucket="bucket.proxyroot.com", Key="tests/test.txt")
    data = object["Body"].read().decode("utf-8")

    assert data == "hello world"
```

Lets run the test

```bash
$ poetry run pytest test_functions.py::test_create_file
=============================================== test session starts ================================================
platform darwin -- Python 3.6.8rc1, pytest-5.4.2, py-1.8.1, pluggy-0.13.1
collected 1 item                                                                                                   

test_functions.py .                                                                                          [100%]

=========================================== 1 passed, 1 warning in 3.36s ===========================================
```

### Reusing fixtures

If you notice that we have been adding the fixtures and tests in the same file and what if we need to re-use the fixtures across the entire project. Here is how we do it.

create a file conftest.py in your folder testing/ and add the fixtures in it

Try running the tests again it will pass.

The code for the above learnings can be found at [pytest-tests](https://github.com/proxyroot/pytest-tests)
