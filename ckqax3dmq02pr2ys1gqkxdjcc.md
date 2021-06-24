## Implementing Twitter Sign-In for Web Apps


*Updates*

> *2015-11-24:* [twitter-sign-in-example](https://github.com/akoskm/twitter-sign-in-example/tree/scribe-2.0) with scribe 2.0

In [#560](https://github.com/fernandezpablo85/scribe-java/issues/560) we had a discussion with [@fernandezpablo][3] about the current state of the [scribe-java][4] API. There are couple of examples already for [pin-based](https://dev.twitter.com/oauth/pin-based) authorization, which is fine for desktop apps, but when it comes to web applications, you probably want something else.

With the current API isn't straightforward how to implement a sign-in process which does not require PIN from the user, but uses the "Authorize this app to use your account" page to request access to the user's Twitter profile.

Both scribe-java ([TwitterExample.java][1]) and twitter4j ([Code Examples][2], skip to _7. OAuth support_) uses these three steps to obtain a new access token:

1. Obtain a request token object
2. Authorize the 3rd party application by entering a PIN, this generates you a Verifier object (no Verifier object in twitter4j, the pin is used directly in the next step as verifier)
3. Convert the  previously obtained request token and verifier to an access token

According to [Implementing Sign in with Twitter][5], the workflow for obtaining an access token without PIN is the following:

1. Obtain a request token object, and define a redirect URL. This will be required in 3.
2. Redirect the user to the authorization URL contained in the request token received in 1. This URL leads to the "Authorize this app to use your account" page
3. User clicks "Sign In"
3. Twitter approves the authorization request and the response is a redirect to the URL supplied in 1. with the following query parameters: **oauth_token**, **oauth_verifier**.
4. According to [Step 3: Converting the request token to an access token][5], everything you need to obtain a request token is **oauth_token** and **oauth_verifier** from the previous redirect request. However, both scribe-java and twitter4j expecting you to supply a request token object in order to [obtain an access token](https://github.com/scribejava/scribejava/blob/1.3.6/src/test/java/org/scribe/examples/TwitterExample.java#L51).

The solution is to manually create a new `Token` by using **oauth_token** as token and **oauth_verifier** as secret in:

```java
/**
 * Default constructor
 *
 * @param token token value. Can't be null.
 * @param secret token secret. Can't be null.
 */
public Token(String token, String secret) {
  this(token, secret, null);
}
```

and a new `Verifier` from **oauth_verifier**:

```java
Token requestToken = new Token(oauthToken, oauthVerifier);
Verifier verifier = new Verifier(oauthVerifier);
```

Finally, retrieve the access token from Twitter:

```java
// POST oauth/access_token
Token accessToken = service.getAccessToken(requestToken, verifier);

// Do something with the access token
OAuthRequest request = new OAuthRequest(Verb.GET, PROTECTED_RESOURCE_URL);
service.signRequest(accessToken, request);
```

This solution is applicable to both scribe-java and twitter4j, because their API is very similar.

I've set up a little demo project showing these steps in action:

[twitter-sign-in-example](https://github.com/akoskm/twitter-sign-in-example)

If you want to discuss, [leave a comment][6] or hit me up on [@akoskm](https://twitter.com/akoskm).

[1]: https://github.com/scribejava/scribejava/blob/1.3.7/src/test/java/org/scribe/examples/TwitterExample.java
[2]: http://twitter4j.org/en/code-examples.html
[3]: https://twitter.com/fernandezpablo
[4]: https://github.com/fernandezpablo85/scribe-java
[5]: https://dev.twitter.com/web/sign-in/implementing
[6]: /2015/07/31/twitter-sign-in-for-web-apps.html#comments
