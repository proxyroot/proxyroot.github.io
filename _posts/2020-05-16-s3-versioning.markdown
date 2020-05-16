---
layout: post
title:  File version control with custom names
description: We will go through a use case that has recently come up to create name for a version of a file. I have decided to use s3 to accomplish this and we will learn how to do this
date:   2020-05-16 12:50:00 +0500
categories: Python pytest pytest-mock moto boto3 s3 aws google-storage-buckets
---

We will go through a use case that has recently come up to create name for a version of a file. I have decided to use s3 to accomplish this and we will learn how to do this.

### Use case

As a user i should be able to save the file with a version name when there are any changes to it.
As a user i should be able to see the list of versions of a file.
As a user i should be able to read the specific version of a file.

### Install

- [boto3](https://github.com/boto/boto3)
- [moto](https://github.com/spulec/moto)
- [pytest](https://github.com/pytest-dev/pytest)
- [pytest-mock](https://github.com/pytest-dev/pytest-mock)

### Idea

S3 keeps versions of a file when ever we update them and i have decided that instead of re-inventing the wheel i'll just use this to accomplish this. However, there is a gotcha! S3 has version id to each version that was created and we need to come up with a solution to persist the names of the versions created so that human/user can see the name instead of version id that he doesn't understand. Since we are already using s3 lets also persist the names with in s3 as well. I've created a meta file that has the data to understand versions that were created for a file. Each user has his own folder where he saves file and has meta file in the same folder.

### Modules

Let's break the use case into small and tiny modules/functions that can acheive a specific goal. I've identified the following

- get_meta_file
- update_meta_file
- get_file
- save_file
- list_versions
- sort_versions

### Tests

Let's do this in a test driven development approach by writing up some tests in tests.py

First thing first, lets write our conftest

```python
import boto3
import pytest

from moto import mock_s3


@pytest.fixture(scope="function")
def _s3():
    with mock_s3():
        conn = boto3.resource("s3", region_name="us-east-1")
        conn.create_bucket(Bucket="bucket.proxyroot.com")
        versioning = conn.BucketVersioning("bucket.proxyroot.com")
        versioning.enable()
        yield {"client": boto3.client("s3", region_name="us-east-1"), "resource": conn}
```

#### test_get_meta_file

When a file path is passed to the function it should return the versions of a file name if it exists otherwise it returns empty list.

```python
from version import (
    list_versions,
    sort_versions,
    get_meta_file,
    save_file,
)


def test_get_meta_file(_s3):
    assert get_meta_file("proxyroot/test.txt") == []
```

### list_versions

Given a path to a file, returns list of versions of that file

```python
def test_list_versions_happy_path(_s3, mocker):
    _s3["client"].put_object(
        Bucket="bucket.proxyroot.com", Key="proxyroot/test.txt", Body="scan stl file",
    )
    expected = [{"created": mocker.ANY, "id": mocker.ANY}]

    assert list_versions("proxyroot/test.txt") == expected

    _s3["client"].put_object(
        Bucket="bucket.proxyroot.com", Key="proxyroot/test.txt", Body="scan stl file",
    )
    expected = [
        {"created": mocker.ANY, "id": mocker.ANY},
        {"created": mocker.ANY, "id": mocker.ANY},
    ]
    assert list_versions("proxyroot/test.txt") == expected
```

#### sort_versions

Given a list of versions sort version sorts based on the created timestamp

```python
def test_sort_versions():
    versions = [
        {"id": "kjksdf", "created": "April 23, 2019"},
        {"id": "asdf", "created": "April 21, 2019"},
    ]
    expected = [
        {"id": "asdf", "created": "April 21, 2019"},
        {"id": "kjksdf", "created": "April 23, 2019"},
    ]
    assert sort_versions(versions) == expected
```

#### save_file

Save a file with the content which creates versions and saves it in meta file. Returns versions list in the end.

```python
def test_save_file(_s3, mocker):
    versions = save_file("proxyroot", "test2.txt", "Hello World", "first")

    assert versions == [
        {"created": mocker.ANY, "name": "first"},
    ]

    versions = save_file("proxyroot", "test2.txt", "Hello World\nHello World", "second")

    assert versions == [
        {"created": mocker.ANY, "name": "first"},
        {"created": mocker.ANY, "name": "second"},
    ]
```

### Implementation

```python
import boto3
import botocore
import json

from datetime import datetime
from dateutil import parser

S3_BUCKET = "bucket.proxyroot.com"


def get_meta_file(folder):
    """
    Given a folder (mark), returns meta file contents

    :return: list<dict>
    """
    key = f"{folder}/meta.json"
    S3 = boto3.resource("s3")
    content_object = S3.Object(S3_BUCKET, key)
    try:
        object = content_object.get()
    except botocore.exceptions.ClientError:
        return []
    return json.loads(object["Body"].read().decode("utf-8"))


def list_versions(key: str):
    """
    Given a string key (proxyroot/test.txt), returns list of versions

    :return: list<dict>
    """
    S3 = boto3.resource("s3")
    versions = S3.Bucket(S3_BUCKET).object_versions.filter(Prefix=key)
    results = []
    for version in versions:
        obj = version.get()
        results.append(
            {"id": obj.get("VersionId"), "created": f'{obj.get("LastModified")}'}
        )
    return results


def sort_versions(versions: str):
    """
    Sort list of dictionaries based on created timestamp in the dictionary

    :return: list<dict>
    """
    return sorted(versions, key=lambda item: parser.parse(item["created"]))


def update_meta_file(folder, name, version_id):
    """
    Given a folder and name of version with version id. Saves the version into meta file

    :return: list<dict>
    """
    S3 = boto3.resource("s3")
    key = f"{folder}/meta.json"
    versions = get_meta_file(folder)
    versions.append({"name": name, "created": f"{datetime.now()}"})
    object = S3.Object(S3_BUCKET, key)
    object.put(Body=json.dumps(versions))
    return versions


def save_file(folder, file_name, content, name):
    """
    Given a folder, file_name and content and version name, saves the file in s3 and updates meta file

    :return: list<dict>
    """
    S3 = boto3.resource("s3")
    key = f"{folder}/{file_name}"
    object = S3.Object(S3_BUCKET, key)
    object.put(Body=content)
    versions = sort_versions(list_versions(key))
    return update_meta_file(folder, name, versions[-1]["id"])
```

### Test


Let's run the tests

```bash
$ poetryrun pytest test.py -s -vv
=============================================== test session starts ================================================
platform darwin -- Python 3.6.8rc1, pytest-5.4.2, py-1.8.1, pluggy-0.13.1 -- /Users/proxyroot/Library/Caches/pypoetry/virtualenvs/s3versioning-py3.6/bin/python
cachedir: .pytest_cache
rootdir: /Users/proxyroot/personal/s3versioning
plugins: mock-3.1.0
collected 4 items                                                                                                  

test.py::test_get_meta_file PASSED
test.py::test_list_versions_happy_path PASSED
test.py::test_sort_versions PASSED
test.py::test_save_file PASSED

================================================ 4 passed in 0.45s =================================================
```

Hooray! :tada:

This is exactly what we can do to persist the names of the versions in s3. No other persistence is required apart from what we already used here `S3`.

### Alternative persistence layer

You can also accomplish this in a few other ways.

- [Github](https://github.com/) (It already has commit hash that you can use to get the file and save the file. You can also persist names in a gist or meta file in the same repo)
- [Google Buckets](https://cloud.google.com/storage/docs/json_api/v1/buckets) (Well by the name of it copy.deepcopy("Amazon S3 Bucket") == "Google Buckets")

Well any service that provides versioning can be used but these are the things that i feel are more reliable, fast and easy to do.

### Note

I haven't implemented the get_file function. I'll leave it for you to write for yourself. You can find the code for the above at [s3versioning](https://github.com/proxyroot/s3versioning)
