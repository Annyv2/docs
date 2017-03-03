---
title: Authenticate
description: This tutorial will show you how to use the Auth0 Python SDK to add authentication and authorization to your API.
---

You can get started by either downloading the seed project or if you would like to add Auth0 to an existing application you can follow the tutorial steps.


<%= include('../../_includes/_package', {
  org: 'auth0-samples',
  repo: 'auth0-python-api-samples',
  path: '00-Starter-Seed',
  requirements: [
    'Python 2.7',
    'Flask 0.10.1',
    'Flask-Cors 2.1.0',
    'Python-Jose 1.3.2'
  ]
}) %>


**If you have an existing application, please follow the steps below.**

### 1. Add the needed dependencies

In this example, we'll be using Flask and we'll be validating the JWT. For that, add the following dependencies to your `requirements.txt`.

```python
flask
python-jose
flask-cors
```

### 2. Create the JWT Validation decorator

Now, you need to validate the access token. Here we take an access token signed with RS256 and validate it by taking the apropriate key from the `/jwks.json` endpoint of your Auth0 domain. We also verify that the API's audience and the token issuer are valid.

```python
import json
import os
import urllib

from dotenv import Dotenv
from functools import wraps
from flask import Flask, request, jsonify, _app_ctx_stack
from flask_cors import cross_origin
from jose import jwt

try:
    env = Dotenv('./.env')
    auth0_domain = env['AUTH0_DOMAIN']
    api_audience = env['API_ID']
except IOError:
    env = os.environ

app = Flask(__name__)


# Format error response and append status code.
def handle_error(error, status_code):
    resp = jsonify(error)
    resp.status_code = status_code
    return resp


def requires_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        auth = request.headers.get('Authorization', None)
        if not auth:
            return handle_error({'code': 'authorization_header_missing',
                                'description':
                                    'Authorization header is expected'}, 401)

        parts = auth.split()

        if parts[0].lower() != 'bearer':
            return handle_error({'code': 'invalid_header',
                                'description':
                                    'Authorization header must start with'
                                    'Bearer'}, 401)
        elif len(parts) == 1:
            return handle_error({'code': 'invalid_header',
                                'description': 'Token not found'}, 401)
        elif len(parts) > 2:
            return handle_error({'code': 'invalid_header',
                                'description': 'Authorization header must be'
                                 'Bearer + \s + token'}, 401)

        token = parts[1]
        jsonurl = urllib.urlopen('https://'+auth0_domain+'/.well-known/jwks.json')
        jwks = json.loads(jsonurl.read())
        unverified_header = jwt.get_unverified_header(token)
        rsa_key = {}
        for key in jwks['keys']:
            if key['kid'] == unverified_header['kid']:
                rsa_key = {
                    'kty': key['kty'],
                    'kid': key['kid'],
                    'use': key['use'],
                    'n': key['n'],
                    'e': key['e']
                }
        if rsa_key:
            try:
                payload = jwt.decode(
                    token,
                    rsa_key,
                    algorithms=unverified_header['alg'],
                    audience=api_audience,
                    issuer='https://'+auth0_domain+'/'
                )
            except jwt.ExpiredSignatureError:
                return handle_error({'code': 'token_expired',
                                    'description': 'token is expired'}, 401)
            except jwt.JWTClaimsError:
                return handle_error({'code': 'invalid_claims',
                                    'description': 'incorrect claims, please check the audience and issuer'}, 401)
            except Exception:
                return handle_error({'code': 'invalid_header',
                                    'description': 'Unable to parse authentication'
                                    ' token.'}, 400)

            _app_ctx_stack.top.current_user = payload
            return f(*args, **kwargs)
        return handle_error({'code': 'invalid_header',
                             'description': 'Unable to find appropriate key'}, 400)    
    return decorated
```
### 3. Use this decorator in your methods

Now, you can just decorate any route's method that requires authentication

${snippet(meta.snippets.use)}

### 4. Call Your API

You can now make requests against your secure API by providing the Authorization header in your requests with a valid JWT access_token.

```har
{
  "method": "GET",
  "url": "http://localhost:8000/path_to_your_api",
  "headers": [
    { "name": "Authorization", "value": "Bearer YOUR_ACCESS_TOKEN_HERE" }
  ]
}
```

### 5. You're done!

Now you have both your frontend and backend configured to use Auth0. Congrats, you're awesome!
