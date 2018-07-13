# Web requests
Save and retrieve progress for creating a web request

## Create a user
> Request example

```json
{
	"email": "demo@getpawprint.com",
	"first_name": "Cecil",
	"last_name": "Harvey",
	"phone": "+14255556174",
	"street_address": "123 Main St",
	"street_address2": "Apt 101",
	"city": "Bellevue",
	"state": "WA",
	"zip": "98004",
	"country": "United States"
}
```

> Response example

```json
{
	"id": "XOKQ-n5zjba1lM1-pOncpGHMaAxoqTGl"
}
```

Returns an external ID (unique random string) that identifies the `web_user` in subsequent requests;
this is so people can't easily guess random `web_user`s and modify their information.

### HTTP Request
`POST /web_request/user`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
first_name | string? | User's first name.
last_name | string? | User's last name.
email | string | Required. User's email address.
phone | string? | User's phone number.
street_address | string? | Line 1 of the user's street address.
street_address2 | string? | Line 2 of the user's street address.
city | string? | User's city.
state | string? | User's state.
country | string? | User's country.

## Update a user
> Request example

```json
{
	"first_name": "Cecil",
	"last_name": "Harvey",
	"phone": "+14255556174",
	"street_address": "123 Main St",
	"street_address2": "Apt 101",
	"city": "Bellevue",
	"state": "WA",
	"zip": "98004",
	"country": "United States"
}
```

> Response example

```json
{
	"id": "XOKQ-n5zjba1lM1-pOncpGHMaAxoqTGl"
}
```

Returns a unique random string that identifies the `web_user` in subsequent requests (an external identifier);
this is so people can't easily guess random `web_user`s and modify their information. Note that email cannot be changed.

### HTTP Request
`PUT /web_request/user/:web_user_external_id`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
first_name | string? | User's first name.
last_name | string? | User's last name.
phone | string? | User's phone number.
street_address | string? | Line 1 of the user's street address.
street_address2 | string? | Line 2 of the user's street address.
city | string? | User's city.
state | string? | User's state.
country | string? | User's country.

## Create or update pet
> Request example

```json
{
	"name": "Gizmo",
	"species": "Dog",
	"breed": "Yorkshire Terrier",
	"gender": "M"
}
```

> Response example

```json
{
	"id": 1
}
```

Returns the ID of the `web_pet`. If the `name` parameter matches an existing pet owned by the user, it is updated;
otherwise a new pet is created and linked to the `web_user`.

### HTTP Request
`POST /web_request/user/:web_user_external_id/pets`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
name | string | Required. Pet's name.
species | string? | Pet's species, e.g. "Dog", "Cat", "Rabbit", etc.
breed | string? | Pet's breed, e.g. "Chihuahua" or "Domestic Short Hair"
gender| string? | 'M' for male or 'F' for female.

## Create a new request
> Request example

```json
{
	"web_user_id": 1,
	"web_pet_ids": [ 2, 5 ],
	"place_id": 1337,
	"checkout_type": 1,
	"reason": "Need proof of vaccination"
}
```

> Response example

```json
{
	"id": "abZj4sT839fsbCdiut8qwV"
}
```

Creates a partial or full records request (without submitting). Submitting the request is a separate API call.
The request is assigned a unique random string, which is returned to the caller and can be used to
retrieve or update the session.

### HTTP Request
`POST /web_request`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
web_user_id | int | Required. User ID created by the `/web_request/user` call.
web_pet_ids | array of ints | Optional. List of web_pet IDs.
place_id | int | Optional. ID from the `place` table.
checkout_type | int | Optional. ID from the `product` table.
reason | string | Optional. User's reason for the records request.

## Update a request
> Request example

```json
{
	"web_pet_ids": [ 2, 5 ],
	"place_id": 1337,
	"checkout_type": 1,
	"reason": "Need proof of vaccination",
	"consent": "https://s3.aws.amazon.com/pawprint/consent_101.pdf",
	"signature": "https://s3.aws.amazon.com/pawprint/signature_101.pdf"
}
```

> Response example

```json
(none)
```

Updates a partial or full records request (without submitting). Submitting the request is a separate API call.
Requests are assigned a unique random string which is used as the request identifier.

### HTTP Request
`PUT /web_request/:webrequestid`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
web_pet_ids | array of ints? | Optional. List of web_pet IDs. The previously stored list is completely overwritten by this list.
place_id | int? | Optional. ID from the `place` table.
checkout_type | int? | Optional. ID from the `product` table.
reason | string? | Optional. User's reason for the records request.
consent | string? | Optional. Link to the consent form.
signature | string? | Optional. Link to the user's electronic signature.

## Get existing request

> Response example

```json
{
	"web_user_id": 1,
	"web_pet_ids": [ 2, 5 ],
	"place_id": 1337,
	"checkout_type": 1,
	"reason": "Need proof of vaccination",
	"consent": "https://s3.aws.amazon.com/pawprint/consent_101.pdf",
	"signature": "https://s3.aws.amazon.com/pawprint/signature_101.pdf"
}
```

Gets an existing web request.
Requests are assigned a unique random string which is used as the request identifier.

### HTTP Request
`GET /web_request/:webrequestid`

## Submit a (complete) request
> Request example

```json
{
	"stripe_token": "tk_8STubeEqtr8i"
}
```

> Response example

```json
(none)
```

Submits a complete request. A request is complete if it contains:

- A complete web_user object
- One or more complete pet objects
  - If one or more pet objects are not complete, request creation will not succeed
- A reason for the request
- A completed consent form
- An electronic signature
- A place

Once the request has been validated, the following actions will be performed:

1. Create a "ghost" `user` from the `web_user` in the request. This has the same attributes as a regular user account, except the `type` is 5
and the user has no password. Hence, they will not be able to log in to the app, but they should be prompted to set a password
and finish setting up their account.
2. Create `pet`s from the `web_pet`s in the request. These are the same as any other pet in the database.
3. Create the appropriate `user_pet` relationships. These are the same as any other `user_pet` relationship in the database.
4. Create the `request` in the database. This is the same as any other request in the database.

### HTTP Request
`POST /web_request/:webrequestid`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
stripe_token | string? | Optional. Stripe token to charge if this was a paid request.
