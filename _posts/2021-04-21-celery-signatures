---
layout: post
title:  Understanding celery signatures .s() method
description: A few cases on how celery signatures would behave with examples explained.
date:   2021-04-21 14:04:00 +0500
categories: Python github actions celery beat scheduler beat signatures .s() celery-s-method
---

A few cases on how celery signatures would behave with examples explained.

### What are Celery Signatures ?

Signatures are used when you want to send the signature of a task invocation to another process or as an argument to another function.
A signature() wraps the args, kwargs, and execution options of a single task invocation. The wrapped content can be sent to functions or can be serialized to send to the network of celery workers using the broker.

### What happens if one of the task fails during grouping celery tasks ?

Signature Errors are sent back to the caller when join is made on the result from apply async during execution of group tasks.

Lets define a task that raises an error when a value 3 is sent

```python
>>> @worker.task()
>>> def adding_test(value):
>>>     if value == 3:
>>>         raise ValueError("Ha ha ha")
>>>     return 1
```

Executing the task with signature and we do that by using celery group module.

```python
>>> from celery import group
>>> j = group([adding_test.s(n) for n in [1, 2, 3]])
>>> r = j.apply_async()
>>> r.join()
Traceback (most recent call last):
  File "/Users/proxyroot/.pyenv/versions/3.7.2/lib/python3.7/code.py", line 90, in runcode
    exec(code, self.locals)
  File "<console>", line 1, in <module>
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/result.py", line 772, in join
    disable_sync_subtasks=disable_sync_subtasks,
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/result.py", line 228, in get
    on_message=on_message,
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/backends/asynchronous.py", line 202, in wait_for_pending
    return result.maybe_throw(callback=callback, propagate=propagate)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/result.py", line 333, in maybe_throw
    self.throw(value, self._to_remote_traceback(tb))
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/result.py", line 326, in throw
    self.on_ready.throw(*args, **kwargs)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/vine/promises.py", line 244, in throw
    reraise(type(exc), exc, tb)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/vine/five.py", line 195, in reraise
    raise value
ValueError: Ha ha ha
```
Hence the same exception is raised at the caller when join is performed to retreive the results of the tasks.

### Countdown

Countdowns are a way to specify when the task need to be executed in (countdown) seconds. Celery will receive the task and puts an eta on the task based on the countdown. This is helpful in instances where we want to execute a job after n seconds.
For example in a distributed world database connections made are different and each connection writes to master and reads from replica.
Here the failure may happen if you create an object from one worker and reading teh object from another worker. If there is a latency on the database replica. This is called replication lag.
To be on a safe side to avoid any failure in case of replication we execute the task with a count down > replication lag so that the object created is available for the otehr worker.

Using the same task lets put a count down of 10 seconds

```python
>>> j = group([adding_test.s(n) for n in [1, 2, 3]])
>>> r = j.apply_async(countdown=10)
```

##### celery worker log

```bash
Received task: cases.tasks.adding_test[a3061f0d-14fe-4ee6-bca1-0eeefa2d3f11]  ETA:[2021-04-21 17:46:53.134310+00:00]
Received task: cases.tasks.adding_test[1230f631-7755-44ef-a14e-a306f576ca9b]  ETA:[2021-04-21 17:46:53.141552+00:00]
Received task: cases.tasks.adding_test[f231f141-f620-4a4c-abc4-8a3ed075aa2a]  ETA:[2021-04-21 17:46:53.147004+00:00]
2021-04-21T17:46:53.148244Z [info     ] task_succeed                   [django_structlog.celery.receivers] dd.span_id=11803132957467772696 dd.trace_id=11892620870047625249 result=1 task_id=1230f631-7755-44ef-a14e-a306f576ca9b
2021-04-21T17:46:53.148280Z [info     ] task_succeed                   [django_structlog.celery.receivers] dd.span_id=12473670402881028930 dd.trace_id=13655196858329145654 result=1 task_id=a3061f0d-14fe-4ee6-bca1-0eeefa2d3f11
2021-04-21T17:46:53.148244Z [info     ] task_succeed                   [django_structlog.celery.receivers] dd.span_id=11803132957467772696 dd.trace_id=11892620870047625249 result=1 task_id=1230f631-7755-44ef-a14e-a306f576ca9b
2021-04-21T17:46:53.148280Z [info     ] task_succeed                   [django_structlog.celery.receivers] dd.span_id=12473670402881028930 dd.trace_id=13655196858329145654 result=1 task_id=a3061f0d-14fe-4ee6-bca1-0eeefa2d3f11
Traceback (most recent call last):
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/app/trace.py", line 385, in trace_task
    R = retval = fun(*args, **kwargs)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/app/trace.py", line 650, in __protected_call__
    return self.run(*args, **kwargs)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/sentry_sdk/integrations/celery.py", line 169, in _inner
    reraise(*exc_info)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/sentry_sdk/_compat.py", line 57, in reraise
    raise value
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/sentry_sdk/integrations/celery.py", line 164, in _inner
    return f(*args, **kwargs)
  File "/Users/proxyroot/candid/kraken/cases/tasks.py", line 55, in adding_test
    raise ValueError("Ha ha ha")
ValueError: Ha ha ha
2021-04-21T17:46:53.162650Z [error    ] task_failed                    [django_structlog.celery.receivers] dd.span_id=3282942238813415681 dd.trace_id=10401334962155923926 error=Ha ha ha task_id=f231f141-f620-4a4c-abc4-8a3ed075aa2a
2021-04-21T17:46:53.162650Z [error    ] task_failed                    [django_structlog.celery.receivers] dd.span_id=3282942238813415681 dd.trace_id=10401334962155923926 error=Ha ha ha task_id=f231f141-f620-4a4c-abc4-8a3ed075aa2a
```

When you do a join we get the result from the worker that executed the task after 10 seconds

```python
>>> r.join()
Traceback (most recent call last):
  File "/Users/proxyroot/.pyenv/versions/3.7.2/lib/python3.7/code.py", line 90, in runcode
    exec(code, self.locals)
  File "<console>", line 1, in <module>
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/result.py", line 772, in join
    disable_sync_subtasks=disable_sync_subtasks,
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/result.py", line 228, in get
    on_message=on_message,
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/backends/asynchronous.py", line 202, in wait_for_pending
    return result.maybe_throw(callback=callback, propagate=propagate)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/result.py", line 333, in maybe_throw
    self.throw(value, self._to_remote_traceback(tb))
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/celery/result.py", line 326, in throw
    self.on_ready.throw(*args, **kwargs)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/vine/promises.py", line 244, in throw
    reraise(type(exc), exc, tb)
  File "/Users/proxyroot/Library/Caches/pypoetry/virtualenvs/kraken-KjM4MU__-py3.7/lib/python3.7/site-packages/vine/five.py", line 195, in reraise
    raise value
ValueError: Ha ha ha
```

### What happens if you do a join() immediately after putting the count down ?

```python
>>> j = group([adding_test.s(n) for n in [1, 2]])
>>> r = j.apply_async(countdown=10)
>>> r.join()
[1, 1]
```

The process that runs join waited for 10 seconds and then fetched the result. Now this is a bad idea if you want to retrieve the results while putting countdown.
In this case signatures is not a good idea as the process waits for countdown seconds. Use apply async with countdown where we don't have to wait for the results.
