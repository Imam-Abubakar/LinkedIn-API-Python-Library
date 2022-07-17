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
- 
__STEP 2: Verifying the App__
- In your App settings, Click on “Verify”.
- Click on “Generate URL”.
- Copy the generated URL in your browser while connected to your Linkedin Account.
- Go back to the verification page and click on “I’m done”.

__STEP 4: Setting up the Redirect URI__
The redirect URI will be used to define which URL the token will be sent to. The redirect URI is important because setting this up to a live site could let an attacker get access to your token. For the purpose of this article,we will set this up to our local computer(localhost). 
- In the OAuth Tab, go to OAuth 2.0 settings.
- In the Authorized redirect URLs for your app, add http://localhost:8080 (It might not be 8080 depending on your configuration so make sure to confirm).
- Copy your configuration information and save it in a credentials.json file that looks like this:
```json
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














## Using the LinkedIn API
