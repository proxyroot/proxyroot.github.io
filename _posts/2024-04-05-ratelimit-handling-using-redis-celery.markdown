---
layout: post
title:  "Handling API Rate Limiting with Redis, Celery and Decorators"
description: Learn how to elegantly handle API rate limiting using Celery and decorators. We will explore a solution to manage request queues between the caller and the API itself, ensuring smooth operations even when facing rate limits.
date:   2024-06-11 10:00:00 +0500
categories: api rate-limiting python celery openai claude sliding-window anthropic llm
---

Handling API rate limiting can be a challenge, especially when you need to manage multiple requests efficiently. Today, we will explore an elegant solution using Celery and decorators to handle rate limiting from APIs on the caller side. We will delve into the sliding window algorithm, understand the problem we are solving, and go through the necessary steps to implement and test this solution.

# Use Case

- Imagine you have a service that interacts with multiple APIs, and you frequently encounter rate limits. Managing these rate limits manually can be cumbersome and error-prone. By using Celery and a custom decorator, we can automate this process and ensure our requests are handled efficiently without exceeding the rate limits.
- Lets design a standalone celery app that acts as a mediator for async requests. It reads a request template using task_id, makes requests and saves response and does a callback.
- We are going to design an elegant and sophisticated solution for two types of problems.
- An API that has a token based limits such as LLM's and also request based limit

# What is a Sliding Window Algorithm?

The sliding window algorithm is a method for managing rate limits by keeping track of the number of requests made within a specified time window. There are two main types of sliding window algorithms:

### Sliding Window with Request Count

This method keeps track of the number of requests made in a fixed time window and resets the count after the window expires.

### Sliding Window Using Token Limit

This method uses tokens to represent available request slots. Tokens are replenished at a fixed rate, and requests consume tokens. If no tokens are available, the request is rate-limited.

# The Problem We Are Trying to Solve

We need a way to handle rate limits automatically, ensuring that our service can make API requests without exceeding the limits. By implementing a decorator to manage rate limits, we can focus on the business logic while the decorator handles the intricacies of rate limiting.

# Implementing the Rate Limiter Decorator

Here's how we can implement a rate limiter decorator using Celery and Redis:

```python
def rate_limiter(
    service_name,
    *,
    token_header,
    requests_header,
    header_ts_parser,
):
    def _token_rate_limiter(func):
        @functools.wraps(func)
        def wrapper(self, *args, **kwargs):
            redis_client = RedisClient()

            seconds = redis_client.get_ttl(service_name)
            if seconds > 1:
                self.retry(ClaudeRateLimitedException(), countdown=seconds)
                return

            # Perform request
            try:
                return func(self, *args, **kwargs)
            except RequestException as e:
                if e.response.status_code == 429:
                    if e.response.headers[requests_header] > 1:
                        # Get request reset from response headers
                        reset_time = e.response.headers[token_header]
                        # Count seconds when we can make next request
                        countdown = header_ts_parser(reset_time)
                        # Set anthropic rate limiting active
                        redis_client.set_key(service_name, True, countdown)
                        # Retry the task after countdown when the limit is reset
                        raise self.retry(exc=e, countdown=countdown)
                    elif e.response.headers[token_header] > 1:
                        # Get token reset from response headers
                        reset_time = e.response.headers[token_header]
                        # Count seconds when we can make next request
                        countdown = header_ts_parser(reset_time)
                        # Set anthropic rate limiting active
                        redis_client.set_key(service_name, True, countdown)
                        # Retry the task after countdown when the limit is reset
                        raise self.retry(exc=e, countdown=countdown)
                # Handle other retry-able error codes
                raise e

        return wrapper

    return _token_rate_limiter
```


# Using the Decorator

Now, let's see how we can use this decorator in our Celery tasks:

- `requests_header` - Used to understand which header holds the timestamp for request limit
- `token_header` - Used to understand which header holds the timestamp for token limit
- `header_ts_parser` - Used to parse the timestamp from different formats, it takes the timestamp as an argument.


```python

@worker.task(
    bind=True,
    autoretry_for=(
        ClaudeRateLimitedException,
    ),
    retry_backoff=True,
    retry_kwargs={"max_retries": 2},
    retry_backoff_max=20,
    retry_jitter=True,
    retry_throw=False,
)
@rate_limiter(
    "anthropic-rate-limiting",
    token_header="anthropic-ratelimit-tokens-reset",
    requests_header="x-ratelimit-reset-requests",
    header_ts_parser=seconds_until_rfc_3339,
)
def anthropic_request(self, task_id: uuid4):
    # Read the request from s3
    s3_path = f"{REQUEST_TEMPLATE_PATH}/{task_id}.request.json"
    request_content = read_file_content(s3_path)
    request_json = json.loads(request_content)
    # Call anthropic for the response
    response_json = anthropic_client.ask(
        endpoint=request_json["endpoint"],
        payload=request_json["payload"],
    )
    # Upload the response from the anthropic to s3
    response_content = json.dumps(response_json)
    response_s3_path = f"{REQUEST_TEMPLATE_PATH}/{task_id}.response.json"
    write_file_content(response_s3_path, response_content)
    # Make any calls to apis or callback to notify of the successful request
    response = requests.post("https://proxyroot.com/requests/callback", json={"task_id": task_id})
    response.raise_for_status()

```

You can design how you want to implement the request read and writes or callbaks based on your needs.

This solution fits others as well where you have requests limit in a time specific window.

# Testing the Decorator

To ensure our decorator works as expected, we can use the requests_mock package for testing:

```python
import json
import uuid
import requests_mock
from datetime import datetime, timedelta
from moto import mock_s3
import boto3
from redis import StrictRedis
from celery.exceptions import Retry
from unittest.mock import patch
from tasks import openai_request, claude_request
from utils.redis_utils import RedisClient

# Mock Redis client
class MockRedisClient:
    def __init__(self):
        self.store = {}

    def set_key(self, key, value, ttl):
        self.store[key] = (value, datetime.utcnow() + timedelta(seconds=ttl))

    def get_ttl(self, key):
        if key in self.store:
            value, expiry = self.store[key]
            if datetime.utcnow() < expiry:
                return int((expiry - datetime.utcnow()).total_seconds())
        return -1


@mock_s3
def test_openai_request_limit():
    # Mock S3 setup
    s3 = boto3.client('s3', region_name='us-east-1')
    s3.create_bucket(Bucket='mybucket')
    task_id = str(uuid.uuid4())
    request_data = {
        "model": "text-davinci-003",
        "messages": [{"role": "user", "content": "Hello, world!"}],
        "temperature": 0.5
    }
    s3.put_object(Bucket='mybucket', Key=f'requests/{task_id}.request.json', Body=json.dumps(request_data))
    
    with requests_mock.Mocker() as m:
        m.post('https://api.openai.com/v1/engines', status_code=429, headers={
            'anthropic-ratelimit-tokens-reset': '60',
            'anthropic-ratelimit-requests-remaining': '0'
        })
        with patch('tasks.RedisClient', MockRedisClient):
            try:
                openai_request(task_id)
            except Retry:
                pass
    
    response_exists = s3.head_object(Bucket='mybucket', Key=f'requests/{task_id}.response.json')
    assert response_exists


@mock_s3
def test_claude_request_limit():
    # Mock S3 setup
    s3 = boto3.client('s3', region_name='us-east-1')
    s3.create_bucket(Bucket='mybucket')
    task_id = str(uuid.uuid4())
    request_data = {
        "endpoint": "completions",
        "payload": {"prompt": "Hello, world!"}
    }
    s3.put_object(Bucket='mybucket', Key=f'requests/{task_id}.request.json', Body=json.dumps(request_data))
    
    with requests_mock.Mocker() as m:
        m.post('https://api.anthropic.com/v1/engines', status_code=429, headers={
            'x-ratelimit-reset-requests': '60',
            'anthropic-ratelimit-tokens-reset': '0'
        })
        with patch('tasks.RedisClient', MockRedisClient):
            try:
                claude_request(task_id)
            except Retry:
                pass
    
    response_exists = s3.head_object(Bucket='mybucket', Key=f'requests/{task_id}.response.json')
    assert response_exists


@mock_s3
def test_openai_request_backoff():
    # Mock Redis and S3 setup
    redis_client = MockRedisClient()
    redis_client.set_key('open_ai-rate-limiting', True, 60)
    s3 = boto3.client('s3', region_name='us-east-1')
    s3.create_bucket(Bucket='mybucket')
    task_id = str(uuid.uuid4())
    request_data = {
        "model": "text-davinci-003",
        "messages": [{"role": "user", "content": "Hello, world!"}],
        "temperature": 0.5
    }
    s3.put_object(Bucket='mybucket', Key=f'requests/{task_id}.request.json', Body=json.dumps(request_data))
    
    with patch('tasks.RedisClient', lambda: redis_client):
        try:
            openai_request(task_id)
        except Retry:
            pass

    response_exists = s3.head_object(Bucket='mybucket', Key=f'requests/{task_id}.response.json')
    assert not response_exists


@mock_s3
def test_claude_request_backoff():
    # Mock Redis and S3 setup
    redis_client = MockRedisClient()
    redis_client.set_key('anthropic-rate-limiting', True, 60)
    s3 = boto3.client('s3', region_name='us-east-1')
    s3.create_bucket(Bucket='mybucket')
    task_id = str(uuid.uuid4())
    request_data = {
        "endpoint": "completions",
        "payload": {"prompt": "Hello, world!"}
    }
    s3.put_object(Bucket='mybucket', Key=f'requests/{task_id}.request.json', Body=json.dumps(request_data))
    
    with patch('tasks.RedisClient', lambda: redis_client):
        try:
            claude_request(task_id)
        except Retry:
            pass

    response_exists = s3.head_object(Bucket='mybucket', Key=f'requests/{task_id}.response.json')
    assert not response_exists


```

# Conclusion

By using Celery and a custom decorator, we can handle API rate limiting efficiently and elegantly. This approach allows us to focus on the core functionality of our services while ensuring that we respect API rate limits. The sliding window algorithm helps manage the rate limits effectively, and testing ensures our solution works as expected.

Feel free to implement this solution in your projects and adapt it to your specific needs. Happy coding!

Don't forget to leave comment :)
