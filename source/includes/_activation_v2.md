# Activation V2
For inviting, onboarding and account linking.

## Find available vet integrations for user

> Response example

```json
[{
  "vet_name": "Pawprint Demo Veterinary",
  "vet_banner": "https://s3.aws.amazon.com/pawprint/banner.png",
  "users": [{
    "vet_user_id": "101",
    "vet_user_first_name": "John",
    "vet_user_last_name": "Smith",
    "vet_user_email": "demo@getpawprint.com",
    "vet_user_phone": "555-555-1234"
   }]
}]
```

Checks for available vet integrations for a user. Looks for the user's email address and phone number in the
`vet_user` table (that haven't been linked already, i.e. `vet_user.user_id` is null) and returns matching vet integrations.

If the same email address or phone number is used for multiple accounts within the same vet (e.g. husband/wife accounts),
then the "users" array will contain multiple elements.
If the same email address or phone number is used for multiple accounts across different vets, (e.g. one pet, multiple vets)
then the root-level array will contain multiple elements.

### HTTP Request
`GET /user/v2/activation/vet_integrations`

## Verify and link existing account
> Request example

```json
{
	"vet_user_id": "101",
	"verification_email": "demo@getpawprint.com",
	"verification_phone": "555-555-1234"
}
```

> Response example

```json
{
  "status": "linked"
}

or

{
  "status": "verification_sent"
}
```

Checks if a verified email address or phone number of the calling user matches the email address or phone number
of the specified `vet_user`.

- If there is a match, the `vet_user` account is linked with the `user` account.
`vet_user_id` should be obtained from the "Find available vet integrations for user" call described above.
- If there is no match, a verification message is sent to `verification_email` or `verification_phone`.
At least one of those fields must be specified; if both are specified, then both an email and an SMS are sent.

### HTTP Request
`POST /user/v2/activation/check_link`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_id | int | ID from the `vet_user` table.
verification_email | string? | If there is no match, send a verification message to this email address.
verification_phone | string? | If there is no match, SMS a verification message to this phone number.

## Send verification link for new accounts
> Request example

```json
{
  "vet_user_id": 101,
	"verification_email": "demo@getpawprint.com",
	"verification_phone": "555-555-1234"
}
```

> Response example

```json
(none)
```

Generates a verification token on the backend, then sends a verification link to `verification_email` or `verification_phone`.
At least one of those fields must be specified; if both are specified, then both an email and an SMS are sent.

### HTTP Request
`POST /user/v2/activation/verify`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_id | int | ID from the `vet_user` table.

## Verify token and link accounts
> Request example

```json
{
  "vet_user_id": 101,
  "token": "6d43b6a1-2154-42de-9961-1a35396cdef8"
}
```

> Response example

```json
(none)
```

**For an existing user.** Verifies a token and adds the user's email and/or phone to the list of verified emails/phones.
Then links the user's account to the given vet_user account.

### HTTP Request
`POST /user/v2/activation/verify_link`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_id | int | ID from the `vet_user` table.
token | string | Token from verification link.

## Verify token and create account
> Request example

```json
{
  "vet_user_id": 101,
  "token": "6d43b6a1-2154-42de-9961-1a35396cdef8"
}
```

> Response example

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NTE5ODUsImlhdCI6MTUyNDg2OTQ1M30.ryzei351HyZrdksi9aa51rtkDH-s2sALaouuhsZug-A",
  "expires": "2018-04-15T00:00:00"
}
```

**For creating a user account from a vet_user account.** Verifies a token and adds the user's email and/or phone to the list of verified emails/phones.
Then creates a user account from the vet_user account. Returns an auth token for the newly created user account.

### HTTP Request
`POST /user/v2/activation/verify_create`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_id | int | ID from the `vet_user` table.
token | string | Token from verification link.
