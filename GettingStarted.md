

This tutorial walks you through making a simple OAuth2 request to an OAuth2 provider.

In this case, Google will be used as the auth provider, though the same process should work for any OAuth 2.0 provider. Please [file a bug](http://code.google.com/p/gwt-oauth2/issues/entry?template=Defect%20report%20from%20user) if you find this not to be the case.

This tutorial assumes you've [set up the library for your application](Setup.md) and have some familiarity with [using OAuth 2.0 with Google](http://code.google.com/apis/accounts/docs/OAuth2.html).

# Building the OAuth2 request #

The first step is to build an authentication request. This request defines the auth provider, your provider-assigned client ID.

## Client ID ##
To get a client ID used to authenticate with Google, visit the [Google APIs Console](http://code.google.com/apis/console) and click on the **API Access** tab.

## Redirect URLs ##

When you create an API project at the APIs Console (or elsewhere, for other auth providers), you need to provide a redirect URL (sometimes called a callback URL) which the provider knows is safe to redirect to when the user has granted access to your app.

The redirect page is provided by the library, and must be whitelisted as a redirect URL at the auth provider you choose.

The redirect URL provided by the library is **oauthWindow.html**, and is hosted at the base path of your GWT app. Remember to add this URL to your auth provider's redirect URLs whitelist, otherwise you will only receive errors indicating "the redirect URI does not match."

## Scopes ##
Google APIs additionally require that you specify at least one authentication scope. Scopes correspond to permissions the user is granting to your application. For example, the [Buzz API](http://code.google.com/apis/buzz/v1/using_rest.html) defines [scopes](http://code.google.com/apis/buzz/v1/using_rest.html#scopes) to allow your application to read data, read and write data, or interact with users' photos.

You also need to find the scopes associated with your auth provider to make a request for the corresponding permissions.

To summarize, to allow users to authorize your app, you need to define:

  * The auth provider (in this case, Google)
  * Your uniquely-identifying client ID (found at the APIs Console)
  * Some scopes that you wish you access

With these, you can construct an `AuthRequest` like so:

```
String AUTH_URL = "https://accounts.google.com/o/oauth2/auth";
String CLIENT_ID = "YOUR_CLIENT_ID"; // available from the APIs console
String BUZZ_READONLY_SCOPE = "https://www.googleapis.com/auth/buzz.readonly";
String BUZZ_PHOTOS_SCOPE = "https://www.googleapis.com/auth/photos";

AuthRequest req = new AuthRequest(AUTH_URL, CLIENT_ID)
    .withScopes(READONLY_SCOPE, PHOTOS_SCOPE); // Can specify multiple scopes here
```

# Executing the OAuth2 request #

Once you have your `AuthRequest` configured, get the instance of the `Auth` class, and pass your request to the `login()` method, like so:

```
Auth.get().login(req, new Callback<String, Throwable>() {
  @Override
  public void onSuccess(String token) {
    // You now have the OAuth2 token needed to sign authenticated requests.
  }
  @Override
  public void onFailure(Throwable caught) {
    // The authentication process failed for some reason, see caught.getMessage()
  }
});
```

# What the user sees #

When this method is executed, the user will be shown a popup window where they can choose to grant or deny access to their data.

If they choose to grant access, or have already granted access, the popup window will close and the `onSuccess()` method will fire with the OAuth2 token your app can use to make authenticated requests.

If the user denies access, the callback's `onFailure()` method will be executed with a `RuntimeException` with the message `Could not find access_token in hash error=access_denied`.

If the user closes the popup window, _neither `onSuccess()` nor `onFailure()` will be executed_.

Remember to place code that requires a valid token _inside the callback_, not after the call to `login()`.

Subsequent calls to `login()` with the same `AuthRequest` will result in `onSuccess()` being called immediately with the same token provided before, unless the token has expired, in which case the popup window will be displayed briefly again to get a new non-expired token.