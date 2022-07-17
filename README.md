# LinkedIn-API-Python-Library
## Overview
Welcome to this full scale documentation of how to use the LinkedIn API with Python. Following this, you will learn how to get your OAuth Credentials, authenticate the API using OAuth 2.0 and create LinkedIn posts. You should understand that the LinkedIn Api is limited to users with basic access and this doc you can follow this documentation to make simple posts automation to your public profile.
## Getting Started
As stated this API provides basic access for users as follows
```
w_member_social : With this, an authenticated user can post, comment and like posts
```
```
r_emailaddress : To retrieve your email address
```
```
r_liteprofile : To retrieve your LinkedIn user profile.
```
> You can't do anything else without requesting partner access, which is quite difficult to get

If you are ready to get started, follow right along.

## Connecting to the LinkedIn API

### Getting your OAuth Credentials
To use the LinkedIn API, the first step is to create an app in the LinkedIn Developer Tool. Remember you need to have a LinkedIn account before you decide to use the API. You can create your LinkedIn account [here](https://www.linkedin.com/)

__STEP 1: Creating the App__
- Visit the [LinkedIn Developer Tool](https://www.linkedin.com/developers/) and click on “Create app”.
- Add Your App name, Company Page URL/Name and Logo.
- Click on “Create App” button.

__STEP 2: Verifying the App__
- In your App settings, Click on “Verify”.
- Click on “Generate URL”.
- Copy the generated URL in your browser while connected to your Linkedin Account.
- Go back to the verification page and click on “I’m done”.

__STEP 4: Setting up the Redirect URI__
The redirect URI will be used to define which URL the token will be sent to. The redirect URI is important because setting this up to a live site could let an attacker get access to your token. For the purpose of this article,we will set this up to our local computer(localhost). 
- In the OAuth Tab, go to OAuth 2.0 settings.
- In the Authorized redirect URLs for your app, add http://localhost:8080 (It might not be 8080 depending on your configuration so make sure to confirm).
- Copy your configuration information and save it in a ```credentials.json``` file that looks like this:
```
{
  client_id: "########",
  client_secret: "########",
  redirect_uri: "http://localhost:8080"
}
```
__STEP 5: Request App Products__
- In your App settings, Go to products and ask for permissions to ***Share on LinkedIn***, ***Sign-in with LinkedIn*** and ***Marketing Developer Platform***.

Most likely, your request for ***Marketing Developer Platform*** will not get approved, limiting you to a very few features of the API. But not to worry, you will still be able to use features stated above. 

> __Disclaimer__: _This article covers what you can do with the LinkedIn API without the LinkedIn Marketing Product_

### Authentication With OAuth 2.0
If that you have all the necessary credentials required to authenticate and use the LinkedIn API, you can proceed to using OAuth 2.0 to authenticate your build and use the API. 
OAuth 2.0 is an authorization framework that enables applications — such as Facebook, GitHub, and LinkedIn to obtain limited access to user accounts on an HTTP service. It works by delegating user authentication to the service that hosts a user account and authorizing third-party applications to access that user account.
In this article, you will learn how to authenticate the LinkedIn API using OAuth 2.0. There are code guides that you can follow starting from this section.

__STEP 1: Importing Libraries__
To run the OAuth 2.0 authentication you will need to install and import all these Python libraries: requests, random, string and json.
```
import json
import random
import requests
import string
```

__STEP 2: Reading the LinkedIn Credentials__
Now, let’s create a function that will read the ```credentials.json``` file that we have created in the previous section
```
def read_creds(filename):
    '''
    Store API credentials in a safe place.
    If you use Git, make sure to add the file to .gitignore
    '''
    with open(filename) as f:
        credentials = json.load(f)
    return credentials
 
creds = read_creds('credentials.json')
client_id, client_secret = creds['client_id'], creds['client_secret']
redirect_uri = creds['redirect_uri']
```

__STEP 3: Authorizing The API__
To authorize the API, you will need to generate a CSRF token to prevent cross-site request forgery.
The ```create_CSRF_token()``` function below creates a random string of letters to use as the CSRF Token.

```
def create_CSRF_token():
    '''
    This function generates a random string of letters.
    It is not required by the Linkedin API to use a CSRF token.
    However, it is recommended to protect against cross-site request forgery
    '''
    letters = string.ascii_lowercase
    token = ''.join(random.choice(letters) for i in range(20))
    return token
```

Then, the ```authorize()``` function says what it does. It will open the authentication URL. Once authorized, it will redirect to the redirect URI given (http://localhost:8080).

```
api_url = 'https://www.linkedin.com/oauth/v2'
 
def authorize(api_url,client_id,client_secret,redirect_uri):
    '''
    Make a HTTP request to the authorization URL.
    It will open the authentication URL.
    Once authorized, it'll redirect to the redirect URI given.
    The page will look like an error. but it is not.
    You'll need to copy the redirected URL.
    '''
    # Request authentication URL
    csrf_token = create_CSRF_token()
    params = {
        'response_type': 'code',
        'client_id': client_id,
        'redirect_uri': redirect_uri,
        'state': csrf_token,
        'scope': 'r_liteprofile,r_emailaddress,w_member_social'
        }
 
    response = requests.get(f'{api_url}/authorization',params=params)
 
    print(f'''
    The Browser will open to ask you to authorize the credentials.\n
    Since we have not set up a server, you will get the error:\n
    This site can’t be reached. localhost refused to connect.\n
    This is normal.\n
    You need to copy the URL where you are being redirected to.\n
    ''')
 
    open_url(response.url)
 
    # Get the authorization verifier code from the callback url
    redirect_response = input('Paste the full redirect URL here:')
    auth_code = parse_redirect_uri(redirect_response)
    return auth_code
```
__STEP 4: Getting the Access Token__
The `authorize()` function contains two functions that will need to be defined: `open_url()` that opens the login URL in the browser and `parse_redirect_uri()`that checks the redirect uri and extract the access token from it.

```
 def open_url(url):
    '''
    Function to Open URL.
    Used to open the authorization link
    '''
    import webbrowser
    print(url)
    webbrowser.open(url)
    
def parse_redirect_uri(redirect_response):
    '''
    Parse redirect response into components.
    Extract the authorized token from the redirect uri.
    '''
    from urllib.parse import urlparse, parse_qs
 
    url = urlparse(redirect_response)
    url = parse_qs(url.query)
    return url['code'][0]
```

A third function `save_token()` will be needed to save the access token to your `credentials.json` file. The `save_token()` function will make sure that you don’t need to log in every time you want to use the API.
```
def save_token(filename,data):
    '''
    Write token to credentials file.
    '''
    data = json.dumps(data, indent = 4) 
    with open(filename, 'w') as f: 
        f.write(data)
```
The last function `headers()` that creates the header that will be used in the request made to the API, but will not be used here, but later when querying the LinkedIn API.
```python
def headers(access_token):
    '''
    Make the headers to attach to the API call.
    '''
    headers = {
    'Authorization': f'Bearer {access_token}',
    'cache-control': 'no-cache',
    'X-Restli-Protocol-Version': '2.0.0'
    }
    return headers
```
    














## Using the LinkedIn API
