---
title: Flask return http status 413 content too large.
date: 2025-09-03 09:50:17
categories: Backend
tags: [Python, AWS, Flask]
---

## Context
Recently, we're resolving the blackduck scan security issue, and we never upgrade any flask package version or any code changes. Besides the application works well before. But after this changes, we start to receive http status 413 (content too large) error. It's very wired. Below is the error:

{% image /assets/images/python/flask-413-code.png, width="600px", alt="Flask 413 Content too large Error"%}

In AWS CloudWatch logs, the error message is the same.

{% image /assets/images/python/flask-413-cloudwatch.png, width="900px", alt="Flask 413 Error in AWS CloudWatch"%}

## Root Cause
After some investigation, we found the root cause is the `werkzeug` package version upgrade. The `Flask` package depends on `werkzeug`, and the new version introduced stricter request size limits, resulting in the 413 error.

The `werkzeug` package set the default `max_form_memory_size` to 500kb instead of unlimited from 3.1.0 which published on 2024-11-1. And our application last build and deployment time is before this change. Below is `werkzeug` package source code mentioned.

{% image /assets/images/python/flask-werkzeug-code.png, width="900px", alt="Werkzeug 3.1.3 source code"%}

In the source code the `max_form_memory_size` attribute, we can see the default value is set to 500kb instead of unlimited. In python `werkzeug` official package documentation, we can see the package release date

{% image /assets/images/python/flask-werkzeug-release-date.png, width="900px", alt="Werkzeug Release date"%}

Our `Flask` version is still using `2.2.3`, but the `werkzeug` version has been upgraded to `3.1.3`, which is causing the 413 error. 

Using command to check the dependency of Flask 2.2.3 package.

``` bash
curl -s https://pypi.org/pypi/Flask/2.2.3/json | jq -r '.info.requires_dist[]'
```

{% image /assets/images/python/flask-dependency-version.png, width="900px", alt="Flask 2.2.3 dependency version"%}

In the dependency list, we can see that `werkzeug` is listed with a version constraint of `>=2.2.2`, which means it can use any latest version of `werkzeug`. And we never define the `werkzeug` package version in `requirements.txt` file. That's why cause the issue.

## Solution
To resolve this, we can specify the version of `werkzeug` to be `3.0.6` which is last version of the 3.0.x in requirements.txt file or adjust the `max_form_memory_size` in our application code.

Define the `werkzeug` version in `requirements.txt` file.
``` plaintext
Werkzeug==3.0.6
```

Or adjust the `max_form_memory_size` in our application code.
``` python
from flask import Flask
app = Flask(__name__)
app.config['MAX_FORM_MEMORY_SIZE'] = 1024 * 1024 * 5  # 5MB limit
```
After applying one of the above solutions, the application should no longer return the 413 error for requests within the specified size limit.