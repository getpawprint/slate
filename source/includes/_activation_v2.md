# Activation V2
For inviting, onboarding and account linking.
Expected scenarios:

- User is logged in to an existing Pawprint account. The flow should be:
  1. Call `/user/v2/activation/vet_integrations`
  2. To link an account that has `verified: true`,  call `/user/v2/activation/link_vet_user`, and enter the pet linking flow.
  3. To link an account that has `verified: false`, call `/user/v2/activation/send_link_token` to get a Branch link with a verification token.
  4. Call `/user/v2/activation/validate` with the verification token
  5. Call `/user/v2/activation/link_vet_user`.
  6. Enter the pet linking flow.
- User has an account in `vet_user`, but no Pawprint account. Create a Pawprint account from the vet_user account like this:
  1. Have the user enter an email address or phone number associated with the `vet_user` account.
  2. Call `/activation/vet_integrations` with an email address or phone number, which returns `vet_user_ids`.
  3. Call `/user/v2/activation/send_create_token` with a `vet_user_id`. This sends a Branch link with a verification token to the email/phone associated with the `vet_user` account.
  4. Call `/user/v2/activation/create_from_vet_account` with the verification token, new email and password.
  5. Enter the pet linking flow.

## Find available vet integrations for user

> Response example

```json
[{
	"vet_name": "Pawprint Demo Veterinary",
	"vet_banner": "https://s3.aws.amazon.com/pawprint/banner.png",
	"users": [{
		"vet_user_id": 101,
		"first_name": "John",
		"last_name": "Smith",
		"email": "demo@getpawprint.com",
		"phone": "555-555-1234",
		"verified": true
	 }]
}]
```

Checks for available vet integrations for a user. Looks for the user's email address and phone number in the
`vet_user` table (that haven't been linked already, i.e. `vet_user.user_id` is null) and returns matching vet integrations.
If an entry contains `verified: true` then you can directl call `/v2/activation/send_link_token` without having to verify
the email or phone number first.

If the same email address or phone number is used for multiple accounts within the same vet (e.g. husband/wife accounts),
then the "users" array will contain multiple elements.
If the same email address or phone number is used for multiple accounts across different vets, (e.g. one pet, multiple vets)
then the root-level array will contain multiple elements.

### HTTP Request
`GET /user/v2/activation/vet_integrations`

## Search for email address

> Response example

```json
{
  "status": "user_needs_password"
}
```

Check for the email's existence in various Pawprint and vet tables. Fields searched:

- `user.email` - Primary email on a user account. Sets `user` = `true` and `needs_password` in the response.
- `verified_email` - Secondary email(s) on a user account. Sets `user` = `true` in the response.
- `vet_user.email` - Primary email on a vet PMS account. Sets `vet_user` = `true` in the response.
- `vet_user_email` - Secondary email(s) on a vet PMS account (Vetdata only; Vetter supports only 1 email). Sets `user` = `true` in the response.

Possible statuses:

- `user` - email exists in the `user` table and has a password, or email is in the `verified_email` table. User should sign in with the account.
- `user_needs_password` - email exists in the `user` table and does not have a password (aka "ghost user"). Password should be set for the account.
- `vet_user` - email exists in the `vet_user` table or the `vet_user_email` table. A new Pawprint count should be created, linked to the `vet_user` account.
See "Send account creation token" and "Create user account from vet_user account".
- `unused` - email does not exist anywhere in the Pawprint database and can be used to create a new account.

### HTTP Request
`GET /activation/email`

### GET parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string | Required. The email address to check.

## Send activation link to vet_user

> Request example

```json
{
  "type": "email",
  "email": "demo@getpawprint.com",
  "phone": "4255556172
}
```

> Response example

```json
{
  (none)
}
```

Sends an email to given email address, or SMS to the given phone number, with a Branch link to create
a new Pawprint account based on an existing `vet_user` account. The Branch link will contain:

- verification_token
- The vet_user ID
- `context: vet_user_activation`

### HTTP Request
`POST /activation/vet_user/:vet_user_id/send_token`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
type | string | Either `email` or `phone`.
email | string? | Send verification link to this email address.
phone | string? | Send verification link via SMS to this phone number.

## Create Pawprint user from vet user via token

> Request example

```json
{
  "verification_token": "Qrrbrbirlbel",
  "email": "demo2@getpawprint.com",
  "password": "passw3rd"
}
```

> Response example

```json
{
  (none)
}
```

Creates a Pawprint user by copying data from the given `vet_user`. `verification_token` comes from
a Branch link in the previous verification step, and `email` lets the user pick a different sign-in
email from the one on the `vet_user` account. `password` is required to set up the account.

### HTTP Request
`POST /activation/vet_user/:vet_user_id/verify_token`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
verification_token | string | Either `email` or `phone`.
email | string? | Optional. Allows the user to log in with a different email from their `vet_user` account.
password | string | Password on the new account.

## Link existing user with vet_user
> Request example

```json
{
	"vet_user_id": 101
}
```

> Response example

```json
(none)
```

**For an existing user.** Links a user with a vet_user, not including pets. Each user account has a list of verified email addresses and phone numbers;
if this list does not contain the `vet_user.email` or `vet_user.phone`, HTTP 403 is returned. Emails and phones can be added to the
list of verified emails/phones with other API calls in this section.

### HTTP Request
`POST /user/v2/activation/link_vet_user`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_id | int | ID from the `vet_user` table to link.

## Send account linking token
> Request example

```json
{
	"type": "email",
	"email": "demo@getpawprint.com",
	"vet_user_id": 101
}
```

> Response example

```json
(none)
```

**For an existing user.** Generates a verification token for the email address or phone number, then sends a Branch link containing that token to the
specified email/phone. Only one of `"email"` or `"phone"` should be specified for the `type` parameter.

The Branch link will contain `context: account_link`, `vet_user_id` and `verificationToken` in its payload. `context` can be used
for navigation, and `verificationToken` should be saved and sent for the next call in the flow.

### HTTP Request
`POST /user/v2/activation/send_link_token`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
type | string | `"email"` or `"phone"`.
email | string | email address if `type` was `"email"`.
phone | string | phone number if `type` was `"phone"`.
vet_user_id | int | ID from the `vet_user` table.

## Validate and consume email/phone token
> Request example

```json
{
	"token": "6d43b6a1-2154-42de-9961-1a35396cdef8"
}
```

> Response example

```json
(none)
```

**For an existing user.** Verifies a token and adds the user's email and/or phone to the list of verified emails/phones.

### HTTP Request
`POST /user/v2/activation/validate`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
token | string | Verification token from the Branch link (emailed or SMSed by an earlier call in the flow).

## Find vet integrations for email/phone

> Response example

```json
[{
	"vet_name": "Pawprint Demo Veterinary",
	"vet_banner": "https://s3.aws.amazon.com/pawprint/banner.png",
	"users": [{
		"vet_user_id": 101,
		"first_name": "John",
		"already_linked": false,
		"has_email": true,
		"can_sms": true
	 }]
}]
```

**For creating new users.** Checks for `vet_users` for the given email address or phone number.
Only `email` or `phone` should be specified as a `GET` parameter; if both are specified, only `email` will be taken.
The response includes `vet_user` accounts that are already linked; these are ineligible for account creation.

<aside class="notice">
This is a public API and does not require authentication.
Only the `vet_user`'s `vet_user_id`, first name and account linking status are returned.
</aside>

If the same email address or phone number is used for multiple accounts within the same vet (e.g. husband/wife accounts),
then the "users" array will contain multiple elements.
If the same email address or phone number is used for multiple accounts across different vets, (e.g. one pet, multiple vets)
then the root-level array will contain multiple elements.

### HTTP Request
`GET /activation/vet_integrations`

### GET parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string? | Email address
phone | string? | Phone number
place_id | integer? | Filters search to a single vet (root level array will have at most 1 element)
email_records | boolean? | If true, and there is exactly 1 account with an email address, sends records to the email on file.

## Send account creation token
> Request example

```json
{
  "vet_user_id": 101,
	"type": "email"
}
```

> Response example

```json
(none)
```

**For creating new users.** Generates a verification token for the given `vet_user`.
A Branch link is sent to the email/phone. The Branch link will contain
`context: sign_up`, `verificationToken`, `first_name`, `last_name`, `email`, `phone`, `vet_name` and `vet_banner`
in its payload.
`context` can be used for navigation, and `verificationToken` should be saved and sent for the next call in the flow.
The rest of the Branch link payload is for pre-populating fields in user account creation.

If the `vet_user` is already linked, an HTTP 400 error will be returned.
If `type:email` is specified and the `vet_user` does not have an email address, an HTTP 400 error will be returned.
Same behavior for `type: phone`.

<aside class="notice">
This is a public API and does not require authentication.
</aside>

### HTTP Request
`POST /user/v2/activation/send_create_token`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_id| string | The `vet_user` that will receive the account creation token.
type | string | `"email"` or `"phone"`.

## Create user account from vet_user account
> Request example

```json
{
	"email": "demo@getpawprint.com",
	"token": "6d43b6a1-2154-42de-9961-1a35396cdef8",
	"password": "passw3rd",
}
```

> Response example

```json
{
  "authToken": "auth_token_here",
  "id": 1009
}
```

**For creating new users.** Checks the given `token` against the `vet_user.verification_token` column. If there is a match,
a new user account is created and the token is consumed. An auth token is returned so the user can immediately start using the app.

<aside class="notice">
This is a public API and does not require authentication.
</aside>

### HTTP Request
`POST /user/v2/activation/create_from_vet_account`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string | Email address to be used as the login email.
password | string | New password. This is stored in salted and hashed form on the server.
token | string | Verification token included with the Branch link sent from a previous call in the flow.
