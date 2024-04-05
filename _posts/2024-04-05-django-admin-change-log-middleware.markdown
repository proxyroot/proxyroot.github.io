---
layout: post
title: Implementing Change Log Middleware for Django: A Step-by-Step Guide
description: Lets explore the implementation of a Change Log Middleware for Django, designed to track and log changes made to models and actions within a web application. The middleware intercepts HTTP requests, identifies relevant actions, retrieves object data, and logs changes before and after request processing. Through a step-by-step guide, we outline the initialization, pre-flight processing, model object retrieval, post-flight processing, and logging mechanisms. By integrating this middleware, developers can streamline change tracking and enhance the auditability of their Django applications.
date:   2024-04-05 10:00:00 +0500
<<<<<<< Updated upstream
categories: Django Middleware ChangeLog WebDevelopment Python AuditTrail Logging HTTPRequests ModelChanges ApplicationMaintenance DeveloperTools CodeLogging DevelopmentBestPractices PythonProgramming WebFrameworks TechTutorials SoftwareDevelopment SEO

=======
categories: Django Middleware ChangeLog WebDevelopment Python AuditTrail Logging HTTPRequests ModelChanges ApplicationMaintenance DeveloperTools CodeLogging DevelopmentBestPractices PythonProgramming WebFrameworks TechTutorials SoftwareDevelopment
>>>>>>> Stashed changes
---

In the realm of web development, maintaining a clear and concise record of changes made within an application is vital for various reasons, ranging from auditing to debugging. Django, a high-level Python web framework, provides a flexible middleware system that allows developers to intercept and process HTTP requests and responses. In this tutorial, we'll delve into implementing a Change Log Middleware in Django, step-by-step, to track and log changes made to models and actions within our application.

# Use case

Consider a scenario where you have a Django application with multiple models, and you need to keep track of changes made to these models. This includes not only the creation, update, and deletion of objects but also the specific modifications made to each object attribute. Manually logging these changes across various models can be tedious and error-prone. This is where a middleware solution comes into play.

# Approach

We'll create a middleware named ChangeLogMiddleware that intercepts incoming HTTP requests, analyzes the requested paths, and logs relevant changes based on the actions performed. Here's our approach broken down into steps:

### Step 1: Initialization

We'll start by initializing our middleware class ChangeLogMiddleware with necessary attributes and dependencies. This includes the Django get_response function, trackers for previous objects and change logs, and a reference to the model class responsible for storing change logs (adminchangelog in this case).

```python
class ChangeLogMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Pre flight
        response = self.get_response(request)
        # Post flight
        return response

```

### Step 2: Pre-flight Processing

Within the call_preflight method, we'll intercept incoming requests, parse the requested paths, and identify the actions to be logged. Depending on whether it's a model-level action or an object-specific action, we'll set the appropriate tracker for logging.

```python
import re


def __call__(self, request):
    # Pre flight
    self.call_preflight(request)
    
    response = self.get_response(request)
    # Post flight
    return response


def call_preflight(self, request):
    # Only track POST requests
    if request.method == "POST":
        # Track admin actions using this pattern (The dropdowns in Model Admin)
        action_pattern = r"\/admin\/(?P<app>\w+)\/(?P<model>\w+)\/"
        action_match = re.match(action_pattern, request.path)
        # Track object specific change pattern (The save delete buttons on object /change)
        object_change_pattern = rf"{action_pattern}(?P<id>\d+)\/(?P<action>\w+)\/"
        object_change_match = re.match(object_change_pattern, request.path)

        if action_match:
            # Set tracker and url path info for actions
            self.change_log_tracker = self.add_action_log_entry
            self.path_dict = action_match.groupdict()
        elif object_change_match:
            # Set tracker and url path info for object change
            self.change_log_tracker = self.add_object_change_log_entry
            self.path_dict = object_change_match.groupdict()
```

### Step 3: Model Object Retrieval

In the load_model_object method, we'll extract the relevant model class and retrieve the previous state of the object (if applicable) based on the provided path parameters.

```python
import json

from django.apps import apps
from django.core import serializers


def __call__(self, request):
    # Pre flight
    self.call_preflight(request)
    self.load_model_object()

    response = self.get_response(request)

    # Post flight
    return response


# Our magic sauce
def load_model_object(self):
    # Ensure we can load model object only when we have the keys
    required_keys = ("app", "model", "id")
    if all(key in self.path_dict for key in required_keys):
        # Load model class instead of importing model from the models.py file. You will thank me later :)
        self.model_class = apps.get_model(self.path_dict["app"], self.path_dict["model"])
        try:
            # Find the object using the id
            object_instance = self.model_class.objects.get(pk=self.path_dict["id"])
            # Serialize the object to json. Django provides serialize component which you can use.
            self.previous_object = json.loads(
                serializers.serialize("json", [object_instance])
            )[0]
        except self.model_class.DoesNotExist:
            pass
```

### Step 4: Post-flight Processing

After the main Django view processes the request and generates a response, we'll execute the call_postflight method to log the changes detected during pre-flight processing.

```python
def __call__(self, request):
    # Pre flight
    self.call_preflight(request)
    self.load_model_object()

    response = self.get_response(request)

    # Post flight
    self.call_postflight(request)

    return response

def call_postflight(self, request):
    # Only track if there is an active tracker set
    if self.change_log_tracker:
        self.change_log_tracker(request)

```

### Step 5: Logging Object Changes

For object-specific actions like updates and deletions, we'll implement the add_object_change_log_entry method to analyze changes made to object attributes and log them accordingly.
```python
import json


# We are going to track change/update and delete
def add_object_change_log_entry(self, request):
    data = request.POST

    if self.path_dict["action"] == "change":
        changes = {}

        for column, old_value in self.previous_object["fields"].items():
            try:
                changed_value = data[column]
            except KeyError:
                continue

            if old_value != changed_value:
                changes[column] = {"old": old_value, "new": changed_value}

        if changes:
            self.change_log_class.objects.create(
                actor=request.user,
                app_name=self.path_dict["app"],
                model_name=self.path_dict["model"],
                action=self.path_dict["action"],
                object_id=self.path_dict["id"],
                changes=json.dumps(changes),
            )

    elif self.path_dict["action"] == "delete":
        self.change_log_class.objects.create(
            actor=request.user,
            app_name=self.path_dict["app"],
            model_name=self.path_dict["model"],
            action=self.path_dict["action"],
            object_id=self.path_dict["id"],
            changes=json.dumps(self.previous_object),
        )

```

### Step 6: Logging Action Entries

For model-level actions such as creation or bulk deletions, we'll implement the add_action_log_entry method to log the action along with any relevant data.

```python
import copy


def add_action_log_entry(self, request):
    data = copy.deepcopy(dict(request.POST))
    ignored_keys = {"csrfmiddlewaretoken"}

    for key in ignored_keys:
        if key in data:
            del data[key]


    if data:
        self.change_log_class.objects.create(
            actor=request.user,
            app_name=self.path_dict["app"],
            model_name=self.path_dict["model"],
            action=data.get("action", [""])[0],
            changes=json.dumps(data),
        )
```

Note: Deletes will have two log entries one for the confirmation and other for pre confirmation
To avoid recording two records you can do the following hack in the add_action_log_entry

```python
# Only track confirmed deletes
if data.get("action", [""]) == ["delete_selected"] and data.get("post") != [
    "yes"
]:
    return

```

### Step 7: Middleware Execution

Finally, we'll implement the __call__ method to orchestrate the execution flow of our middleware, ensuring pre-flight processing, object retrieval, main view execution, and post-flight processing in the correct order.

Our middleware looks like this

```python
import copy
import json
import re

from django.apps import apps
from django.core import serializers


class ChangeLogMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        self.previous_object = None
        self.change_log_tracker = None
        self.path_dict = {}
        self.model_class = None
        self.change_log_class = apps.get_model("users", "adminchangelog")

    def call_preflight(self, request):
        # Only track POST requests
        if request.method == "POST":
            # Track admin actions using this pattern (The dropdowns in Model Admin)
            action_pattern = r"\/admin\/(?P<app>\w+)\/(?P<model>\w+)\/"
            action_match = re.match(action_pattern, request.path)
            # Track object specific change pattern (The save delete buttons on object /change)
            object_change_pattern = rf"{action_pattern}(?P<id>\d+)\/(?P<action>\w+)\/"
            object_change_match = re.match(object_change_pattern, request.path)

            if action_match:
                # Set tracker and url path info for actions
                self.change_log_tracker = self.add_action_log_entry
                self.path_dict = action_match.groupdict()
            elif object_change_match:
                # Set tracker and url path info for object change
                self.change_log_tracker = self.add_object_change_log_entry
                self.path_dict = object_change_match.groupdict()

    def load_model_object(self):
        # Ensure we can load model object only when we have the keys
        required_keys = ("app", "model", "id")
        if all(key in self.path_dict for key in required_keys):
            # Load model class instead of importing model from the models.py file. You will thank me later :)
            self.model_class = apps.get_model(self.path_dict["app"], self.path_dict["model"])
            try:
                # Find the object using the id
                object_instance = self.model_class.objects.get(pk=self.path_dict["id"])
                # Serialize the object to json. Django provides serialize component which you can use.
                self.previous_object = json.loads(
                    serializers.serialize("json", [object_instance])
                )[0]
            except self.model_class.DoesNotExist:
                pass

    def call_postflight(self, request):
        # Only track if there is an active tracker set
        if self.change_log_tracker:
            self.change_log_tracker(request)

    def __call__(self, request):
        # Pre flight
        self.call_preflight(request)
        self.load_model_object()

        response = self.get_response(request)

        # Post flight
        self.call_postflight(request)

        return response

    def add_object_change_log_entry(self, request):
        data = request.POST

        if self.path_dict["action"] == "change":
            changes = {}

            for column, old_value in self.previous_object["fields"].items():
                try:
                    changed_value = data[column]
                except KeyError:
                    continue

                if old_value != changed_value:
                    changes[column] = {"old": old_value, "new": changed_value}

            if changes:
                self.change_log_class.objects.create(
                    actor=request.user,
                    app_name=self.path_dict["app"],
                    model_name=self.path_dict["model"],
                    action=self.path_dict["action"],
                    object_id=self.path_dict["id"],
                    changes=json.dumps(changes),
                )

        elif self.path_dict["action"] == "delete":
            self.change_log_class.objects.create(
                actor=request.user,
                app_name=self.path_dict["app"],
                model_name=self.path_dict["model"],
                action=self.path_dict["action"],
                object_id=self.path_dict["id"],
                changes=json.dumps(self.previous_object),
            )

    def add_action_log_entry(self, request):
        data = copy.deepcopy(dict(request.POST))
        ignored_keys = {"csrfmiddlewaretoken"}

        for key in ignored_keys:
            if key in data:
                del data[key]

        # TODO: Check for deletes that dont need confirmation
        # # Only track confirmed deletes
        # if data.get("action", [""]) == ["delete_selected"] and data.get("post") != [
        #     "yes"
        # ]:
        #     return

        if data:
            self.change_log_class.objects.create(
                actor=request.user,
                app_name=self.path_dict["app"],
                model_name=self.path_dict["model"],
                action=data.get("action", [""])[0],
                changes=json.dumps(data),
            )

```

You can enable this middleware in django settings.py

```python
MIDDLEWARE = [
    ...,
    "project.middlewares.ChangeLogMiddleware",
    ...,
]
```

# Conclusion:

Implementing a Change Log Middleware in Django can greatly enhance the maintainability and auditability of your application by automatically logging changes made to models and actions. By following the step-by-step approach outlined in this tutorial, you can seamlessly integrate this functionality into your Django projects, empowering you with comprehensive change tracking capabilities.

Note: I have kept this simple and you can do a lot with this middleware to intercept and trace all the things you want.
Word of Advise: This is an interceptor and this code needs to be very carefully written and thoroughly tested. If something goes wrong your service will be down.
