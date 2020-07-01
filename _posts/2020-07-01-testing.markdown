---
layout: post
title:  Integration, User Acceptance and Performance tests
description: Writing tests should be fun and easy. Testing was like an ocean for me when i first started writing test and i didn't know how to swim. With past few experience i bring some knowledge to you and hope to add value to readers.
date:   2020-07-01 19:57:00 +0500
categories: python pytest selenium uat it pt integration-test performance-test user-acceptance-test chrome
---

Writing tests should be fun and easy. Testing was like an ocean for me when i first started writing test and i didn't know how to swim. With past few experience i bring some knowledge to you and hope to add value to readers.

### Use Case

To run Integration, User Acceptance and Performance tests.

### Integration Test

Integration Testing is a level of software testing where individual units are combined
and tested as a group. The purpose of this level of testing is to expose faults
in the interaction between integrated units.

Writing integration tests depends on how the system is architectured. There is a key
difference between having the synchronus and asynchronus requests to a service.

**Synchronous Requests:**

When a request is made to a service we expect the response immediately and the individual components
talk to each other to return the response. We assert the response based on the return value

**Example:**
**Operation:** Sign up customer
**Request:** Sign up customer
Response: customer

**Asynchronous Requests:**

When a request is made to a service we dont expect immediate response. The individual components
talk to each other to do a specific job. We assert for the list of things that were supposed to happen up on that request

##### Example:
**Operation:** Email user on registration.
**Request:** Sign up customer
#### Tools to do integration tests

### How to write Integration test in python:

```python
import requests

def test_get_customer():
    response = requests.get("http://proxyroot.com/api/v1/customers/1234")
    assert response.status_code == 200
    expected = {"name": "Rob Huffington", "id": 1234, "dob": "2020-07-01"}
    assert response.json() == expected

```

Behind the scenes the above test when pings the customer end point it has to do few things.
- Talk to the persistence layer to fetch the customer information
- Serialize the customer data
- Return the serialized response

Another example for similar test is customer sign up. Things that (lets assume) happen behind the scenes are
- Talk to the persistence layer to create customer if not exists
- Dispatch asynchronous task to send a welcome email
- Dispatch asynchronous task to create/update CRM for the user
- ...

### User Acceptance Test

When a user performs an action they expect to happen something. We generally use selenium or some sort of browser
automation to test this.

##### Example
**Operation:** When a user fills the form and hits signup
**Result:** User is redirected to dashboard

### How to write User Acceptance Test in python:

```python
import pytest
from selenium import webdriver

@pytest.fixture(scope="module")
def driver():
    chrome = webdriver.chrome()
    yield chrome


def test_get_home_page(driver):
    driver.get("https://proxyroot. com")

    # Check if the page is loaded
    assert "Welcome to proxyroot" in driver.page_source

    # Click sign up button
    element = chrome.find_element_by_xpath("//button[contains(text(), 'Signup')]")
    element.click()

    # Check current page url is sign up page and first name text exists in page
    assert driver.current_url.endswith("/signup")
    assert "First Name" in driver.page_source
```

### Performance Test

A system behaves differently in different loads. Questions to ask oneself
- How does a system behave when we hit 100 request at the same time?
- How does a system behave when we hit with 1000 requests?
- How many users am i expecting to interact with the system?

In simple words running integration test on large scale and expecting the system to behave same.

### How to write Performance tests:

Well you can write performance tests in python by launching multi threads to hit a same end point. Though, there is already an existing tool that i felt really helpful for doing performance tests. `JMETER`

- [Download Jmeter](http://jmeter.apache.org/download_jmeter.cgi)
- [A quick tour of jmeter basics](https://www.youtube.com/watch?v=mXGcBvWYl-U)
- [User manual](https://jmeter.apache.org/usermanual/index.html)

Apache JMeter may be used to test performance both on static and dynamic resources, Web dynamic applications. It can be used to simulate a heavy load on a server, group of servers, network or object to test its strength or to analyze overall performance under different load types.
