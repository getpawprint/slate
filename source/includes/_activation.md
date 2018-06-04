# Activation
For inviting, onboarding and account linking.

## Check vet activation code

> Response example

```json
{
  "firstName": "John",
  "lastName": "Smith",
  "vetBanner": "https://s3.aws.amazon.com/pawprint/banner.png",
  "vetName": "Pawprint Demo Veterinary",
  "code": 106539,
  "email": 'johnsmith@example.com',
  "phone": '15555551234'
}
```

Checks if a vet activation code exists. This is for onboarding or linking a vet account. Some vet information
and user information is returned, which can be used to pre-populate fields for account creation.

### HTTP Request
`GET /user/activation/:code`

## Sign up a user
> Request example

```json
{
	"email": "demo@getpawprint.com",
	"password": "p4ssw0rd",
	"lat": 49.1111,
	"lng": -23.1111,
	"firstName": "Demo",
	"lastName": "User",
	"phone": "425-753-6174",

 	// Activation code
	"activation_code": 106539

	// Facebook signup only
	"facebookid": "10700061",
	"access_token": "avcbjhgaWXt"
}
```

> Response example

```json
(none)
```

Signs up a user, according to one of the following flows:

#### Facebook
If a facebook `access_token` and `facebookid` are provided, then the user information is obtained via facebook.

#### Biz invite code
Deprecated scenario. If an `activation_code` is provided, and the code exists in the `activation_code` table where `activation_code.user_id` is not null,
then there should already be a user account created with `type = 4` ("ghost user"). The "ghost user" is activated by setting `type = 1` (regular user)
and setting the password.

#### Vet activation code
If an `activation_code` is provided, and the code exists in the `activation_code` table where `activation_code.user_id` is null,
then `activation_code.vet_user_id` is expected to be not null. User information is copied from the `vet_user` table into the `user` table
and a new account is created.

<aside class="notice">
The activation code will be deleted after the user has been created.
</aside>

#### Regular
If no Facebook information or access code was provided, then a new user account is created.

### HTTP Request
`POST /user/signup`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string | User's email address
password | string | User's password, should be 6 or more characters
firstName | string? | User's first name
lastName | string? | User's last name
phone | string? | User's phone number
lat | number? | User's GPS latitude; appears to be unused (maybe for business?)
lng | number? | User's GPS longitude; appears to be unused (maybe for business?)
facebookid | string? | User's Facebook ID
access_token | string? | Facebook access token provided by the Facebook client
activation_code | string? | Activation code

## Get vet_pets

> Response example

```json
[
    {
        "id": 797204,
        "name": "Ein",
        "birthdate": "2009-06-29T07:00:00.000Z",
        "adoption_date": null,
        "gender": "m",
        "species": "dog",
        "color": null,
        "breed": "Corgi (Pembroke)",
        "weight": null,
        "neuter": true,
        "microchip": "3258889"
    }
]
```

Gets the vet_pets associated with the user. The user must be linked with a vet integration account in order
for this to work.

### HTTP Request
`GET /user/vet_pets`

## Sync existing pet with vet_pet
> Request example

```json
{
  "pet_id": 10,
  "vet_pet_id": 600528
}
```

> Response example

```json
(none)
```

Updates an entry in the `pet` table with data from an existing `vet_pet`. If any field(s) in `pet` are blank, they are filled in
with the corresponding field(s) from `vet_pet`; existing `pet` data is not overwritten.

### HTTP Request
`POST /user/sync_pet`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
pet_id | int | ID from the `pet` table.
vet_pet_id | int | ID from the `vet_pet` table.


## Create pet from vet_pet
> Request example

```json
{
  "vet_pet_id": 600528
}
```

> Response example

```json
(none)
```

Creates a new entry in the `pet` table from an existing `vet_pet`.

### HTTP Request
`POST /user/create_pet`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_pet_id | int | ID from the `vet_pet` table.

## Send vet_user verification link
> Request example

```json
{
  "type": "email"
  "email": "demo@getpawprint.com",
  "phone": "5555551234"
}
```

> Response example

```json
(none)
```

Sends a verification link to a phone number or email address. `type` must be either `email` or `phone`;
and either the `email` or `phone` field is required depending on the value for `type`.
When this endpoint is called, a token is generated in the `vet_user.verification_token` field, and the verification
link (a Branch URL) contains this token.

### HTTP Request
`POST /user/send_vet_user_verification`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
type | string | Either `email` or `phone`.
email | string? | Send verification link to this email address.
phone | string? | Send verification link via SMS to this phone number.

## Verify vet_user token and link account
> Request example

```json
{
  "verification_token": "badf-0339-19def8-c0e0b0f048ee98fc",
  "activation_code": "9989899",
  "verification_type": "sms"
}
```

> Response example

```json
(none)
```

Checks a verification token against the vet_user table and sets its status to verified. Links vet_user account
with an existing user account. The verification token is generated by the `send_vet_user_verification` call,
and the vet_user's activation code is in the `activation_code` table.

### HTTP Request
`POST /user/verify_vet_user`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
verification_token | string | The token in the verification link.
activation_code | string | The activation code for the vet_user.
verification_type | string? | Should be 'email' or 'sms'; for stat tracking only and has no functional effect.
