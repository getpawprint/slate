# User
Functions that have to do with managing users' profiles and settings.

## Create and email password reset token (v1)
> Request example

```json
{
	"email": "demo2@getpawprint.com"
}
```

> Response example

```json
success
```

Sets a reset token on the account specified by the `email` parameter and sends a reset link to it.
The reset token is valid for ***1 hour***.

### HTTP Request
`POST /user/forgot`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string | Email address on the user's account

## Create and email password reset token (v2)
> Request example

```json
{
	"email": "demo2@getpawprint.com",
	"scenario": "web_request"
}
```

> Response example

```json
success
```

Sets a reset token on the account specified by the `email` parameter and sends a reset Branch link to it.
The reset token is valid for ***7 days***.

The Branch link contains:
- `context`: `'reset_password'`, intended for app navigation
- `scenario`: optional parameter from the caller; allows for more flexibility in rendering/navigation. Defaults to `user_forgot`.
- `email`: Intended for rendering; not required to call the password reset endpoint.
- `resetPasswordToken`

If the Branch link is opened on a phone, either the app will be launched or the user will be redirected to the app store.
If it is opened on a desktop browser, the user will be redirected to `/getmobile?resetPasswordToken=${resetPasswordToken}`,
which in turn redirects to `/reset/${resetPasswordToken}` (triggered by the query string parameter).

### HTTP Request
`POST /reset`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string | Email address on the user's account
scenario | string? | Caller-defined optional parameter. Allows for more flexibility in rendering/navigation of password reset UIs. Defaults to `user_forgot`.

## Render reset page for token

> Response example

```
(an HTML web page)
```

Checks the token specified in the `:token` parameter. If it is valid, the rendered web page
allows the user to reset their password. If not, then an error message is displayed.

### HTTP Request
`GET /reset/:token`

## Reset password
> Request example

```json
{
	"password": "passw0rd123"
}
```

> Response example

```json
Password updated
```

Sets a new password on the user account specified by `:token` (compares to `user.resetPasswordToken` field).

### HTTP Request
`POST /reset/:token`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
password | string | The new password

