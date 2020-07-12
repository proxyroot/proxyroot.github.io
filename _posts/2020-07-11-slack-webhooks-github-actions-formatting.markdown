---
layout: post
title:  Taking advantage of Slack Webhooks
description: As a stake holder I would like to know when a software release has been made which includes list of changes categorized by either of the three types (features, maintenance, bug fixes).
date:   2020-07-11 20:39:00 +0500
categories: Python github actions marketplace slack webhook format-slack-message slack-api
---

As a stake holder I would like to know when a software release has been made which includes list of changes categorized by either of the three types (features, maintenance, bug fixes).

### What is a Webhook?

Lets take an example of shipment tracker. Customers want to order some toys online and they would like to receive shipment updates. A shipment company provides a way to subscribe to shipments by allowing the developer to add a webhook url to the shipment. As the shipment moves along the way to destination it status changes and this event will be sent to a handler that calls the webhook url with the status data.

### Idea

When a software release is made in github, all the stake holders need to know about the list of changes that went in the release. An organization which uses slack as their communication platform can utilize slack channels to communicate with stake holders at once. We will use github actions for running the job that notifies slack channel upon publishing a new release.

Gitub actions is a platform which automates, customizes, and executes software development workflows in the repository.

### Implementation

First thing first, lets write a module that sends a slack notification about the release. To do this, we have to use github api to get the latest release and its description. After going through the [Git api documentation](https://developer.github.com/v3/repos/releases/) I quickly wrote this python code to get the json response of latest release.


```python
import requests

from pprint import pprint


GITHUB_TOKEN = os.environ["GITHUB_TOKEN"]

headers = {
    "Authorization": f"token {GITHUB_TOKEN}"
}
response = requests.get("https://api.github.com/repos/proxyroot/ghost/releases/latest", headers=headers)
response.raise_for_status()

pprint(response.json())

{'assets': [],
 'assets_url': 'https://api.github.com/repos/proxyroot/ghost/releases/28472473/assets',
 'author': {...},
 'body': '### Features\r\n'
         '\r\n'
         '- Implement user sign up @proxyroot #312\r\n'
         '- Create webhook callback view @dummyroot #314\r\n'
         '\r\n'
         '### Maintenance\r\n'
         '\r\n'
         '- Squash migrations @dummyroot #313\r\n'
         '\r\n'
         '### Bug fixes\r\n'
         '\r\n'
         '- Add time out to request call @proxyroot  #311\r\n',
 'created_at': '2020-04-27T17:30:00Z',
 'draft': False,
 'html_url': 'https://github.com/proxyroot/ghost/releases/tag/v0.1.0',
 'name': '0.1.0',
 'tag_name': 'v0.1.0',
 ...}

```

Before writing the above code I exported environment variable GITHUB_TOKEN and installed requests python package. [Github Docs](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) shows you how to create a personal access token (For our usecase we only need repo access).

Notice that the body of a release is in markdown syntax? We can send rich slack messages in markdown format using the slack api. A nice thing about the github ui is that it automatically adds links around pr numbers, commit hashes and users. For example in the response body texts such as `#312`, `#313` will be clickable links in github ui. I wanted to send slack message in a same way so that its appealing and easy to access code with references. I want to do two things to the body in response

1. Add pr links to the pr `#number`
2. Add author links to the `@author`

I started looking into regex to wrap text around matches here is how I made the regex for the above two use cases

_Add pr links to the pr `#number`_
```python
import re

pattern = r"#(\d+)"
replacement = r"<https://github.com/proxyroot/ghost/pull/\1|#\1>"
string = "hi #123 hello"
print(re.sub(pattern, replacement, string))
hi <https://github.com/proxyroot/ghost/pull/123|#123> hello
```

_Add author links to the `@author`_
```python
import re

pattern = r"@(\w+)"
replacement = r"<https://github.com/\1|@\1>"
string = "hi @proxyroot hello"
print(re.sub(pattern, replacement, string))
hi <https://github.com/proxyroot|@proxyroot> hello

```

You can also try to enhance the message by adding version number, release sign off member name, release link, etc.,

Now that the message is ready and its time to send it to slack webhook, I [created a slack incoming webhook](https://api.slack.com/messaging/webhooks#create_a_webhook) which gave me a webhook url. I went through the [slack webhook api docs](https://api.slack.com/messaging/webhooks#posting_with_webhooks) and wrote the below code quickly.

```python
data = {
    "blocks": [{"type": "section", "text": {"type": "mrkdwn", "text": "Hello world"}}],
}
url = "<your-webhook-url-goes-here>"
response = requests.post("<<WEBHOOK_URL>>", json=data)
response.raise_for_status()
```

I used [Slack builder](https://app.slack.com/block-kit-builder/) to compose the data json which helped me a lot to make the message rich and see it before event sending it to slack.

It all boils down to the following piece of code after bringing all the pieces of code together and improving it a bit

```python
import os
import re

import requests


GITHUB_TOKEN = os.environ["GITHUB_TOKEN"]
WEBHOOK_URL = os.environ["WEBHOOK_URL"]
REPOSITORY = os.environ["REPOSITORY"]
ICON_URL = os.environ["ICON_URL"]


def notify_slack():
    """
    Call webhook url with release description
    """
    headers = {
        "Authorization": "token %s" % GITHUB_TOKEN,
    }
    response = requests.get(
        "https://api.github.com/repos/%s/releases/latest" % REPOSITORY,
        headers=headers,
    )
    response.raise_for_status()
    release = response.json()
    message = "*Release:*  <%s|%s>\n\n%s" % (release['html_url'], release['tag_name'], release['body'])
    # Format message to add pr links
    message = re.sub(
        r"#(\d+)", r"<https://github.com/%s/pull/\1|#\1>" % REPOSITORY, message,
    )
    # Format message to add author links
    message = re.sub(r"@(\w+)", r"<https://github.com/\1|@\1>", message)
    # Call webhook with message
    data = {
        "blocks": [{"type": "section", "text": {"type": "mrkdwn", "text": message}}],
        "icon_url": ICON_URL,
    }
    response = requests.post(WEBHOOK_URL, json=data)
    response.raise_for_status()


if __name__ == '__main__':
    notify_slack()

```

### Integrating with GitHub Actions

Let's define a workflow that could run the above script when a release is published on the repository. Used [github secrets](https://docs.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets) to add GITHUB_TOKEN and WEBHOOK_URL values. Created file .github/workflows/slack-notification.yml in my repository root folder which looks like this

```yml
-- .github/workflows/slack-notification.yml

name: Send release notes to Slack

on:
  release:
    types: [published]

jobs:
  notify:
    runs-on: ubuntu-latest

    steps:
      - name: Compose message and send
        env:
          WEBHOOK_URL: `"${{ secrets.WEBHOOK_URL }}"`
          GITHUB_TOKEN: `"${ { secrets.GITHUB_TOKEN } }"`
          GH_REPO: proxyroot/ghost
          ICON_URL: https://proxyroot.com/icon.png
        run: |
          pip install requests
          python notify_slack.py
```

Thats it! We can always improve the code and add features. Do checkout [GitHub release drafter](https://github.com/marketplace/actions/release-drafter) which can help you draft a release before it gets published. With release drafter you can auto generate the release description/body and dont have to worry about writing it manually. Once you use release drafter you will love pr labels.

Cheers :tada:
