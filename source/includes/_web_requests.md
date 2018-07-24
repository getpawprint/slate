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
	"gender": "M",
	"birthdate": "2018-01-01"
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
birthdate | datetime? | Pet's birth date

## Delete a web pet from a request
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Unlinks a web_pet from its web user and web_request, then deletes it.

### HTTP Request
`DELETE /web_request/user/:web_user_external_id/pets/:web_pet_id`

## Create a new request
> Request example

```json
{
	"web_user_external_id": "aXdJG09jq1sTpiV",
	"web_pet_ids": [ 2, 5 ],
	"place_id": 1337,
	"product_id": 1,
	"reason": "Need proof of vaccination",
	"stripe_token": "tk_8STubeEqtr8i",
	"signature": "https://s3.aws.amazon.com/pawprint/signature_101.pdf",
	"promocode": "mars2018",
	"note": "Used to be named Russell"
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
web_user_external_id | int | Required. User ID created by the `/web_request/user` call.
web_pet_ids | array of ints | Optional. List of web_pet IDs.
place_id | int | Optional. ID from the `place` table.
product_id | int | Optional. ID from the `product` table.
reason | string? | Optional. User's reason for the records request.
stripe_token | string? | Optional. Payment information (not charged until the request is submitted).
signature | string? | Optional. Link to the user's electronic signature.
promocode | string? | Optional. Promotion code; its effect appears in previews and will be applied when the request is submitted.
note | string? | Optional. Note to vet.

## Update a request
> Request example

```json
{
	"web_pet_ids": [ 2, 5 ],
	"place_id": 1337,
	"product_id": 1,
	"reason": "Need proof of vaccination",
	"stripe_token": "tk_8STubeEqtr8i",
	"signature": "https://s3.aws.amazon.com/pawprint/signature_101.pdf",
	"promocode": "mars2018",
	"note": "Used to be named Russell"
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
product_id | int? | Optional. ID from the `product` table.
reason | string? | Optional. User's reason for the records request.
stripe_token | string? | Optional. Payment information (not charged until the request is submitted).
signature | string? | Optional. Link to the user's electronic signature.
promocode | string? | Optional. Promotion code; its effect appears in previews and will be applied when the request is submitted.
note | string? | Optional. Note to vet.

## Get existing request

> Response example

```json
{
	"web_user": {
		"id": "aXc984htjlspu",
		"first_name": "Web",
		"last_name": "User",
		"email": "webuser@getpawprint.com",
		"city": "Bellevue",
		"state": "WA",
	},
	"web_pets": [
		{
			"id": 1,
			"name": "Webby",
			"species": "Dog",
			"gender": "M"
		},
		{
      "id": 2,
      "name": "Chewy",
      "species": "Dog",
      "gender": "F"
		}],
	"place_id": 1337,
	"product_id": 8,
	"reason": "Need proof of vaccination",
	"stripe_token": "tk_8STubeEqtr8i",
	"signature": "https://s3.aws.amazon.com/pawprint/signature_101.pdf"
}
```

Gets an existing web request. Complete objects (instead of IDs) are returned for `web_user` and `web_pet`.
Requests are assigned a unique random string which is used as the request identifier.

### HTTP Request
`GET /web_request/:webrequestid`

## Get consent form

> Response example

```json
{
  "url": "https://s3.aws.amazon.com/buckets/pawprint/consent/consent_4253478.pdf"
}
```

Generates a consent form for a complete request. A request is complete if it contains:

- A complete web_user object
- One or more complete pet objects
  - If one or more pet objects are not complete, request creation will not succeed
- A reason for the request
- An electronic signature
- A place

### HTTP Request
`GET /web_request/:webrequestid/consent`

## Preview a (complete) request

> Response example

```json
{
  "items": [
    {
        "id": 9,
        "description": "Request for full records for Apple from Animal Care Center",
        "cost": 9.99
    },
    {
        "id": 10,
        "description": "Additional full records for Bowser from Animal Care Center",
        "cost": 4.99
    }
  ],
  "subtotal": 14.98,
  "tax": 0,
  "discount": -1,
  "total": 13.98
}
```

Previews a complete request. A request is complete if it contains:

- A complete web_user object
- One or more complete pet objects
  - If one or more pet objects are not complete, request creation will not succeed
- A reason for the request
- An electronic signature
- A place

If the request is valid, then an order preview object is returned with a price breakdown.

### HTTP Request
`GET /web_request/:webrequestid/preview`

## Get consent form for a (complete) request

> Response example

```json
{
  "url": "https://pawprint-web-request-consent.s3.amazonaws.com/5HhfWf_34JyX_lzFjCrJ5rWWivpZJstM.pdf"
```

Validates a request (see above for rules), generates a consent form and returns the URL to the consent form. Subsequent calls
will overwrite the previous consent form for the request.

### HTTP Request
`GET /web_request/:webrequestid/consent`


## Submit a (complete) request
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Validates a request (see above for rules), and performs the following actions:

1. Checks for a Stripe token for payment, if it's a paid request
2. Charges the Stripe token (if applicable)
3. Create a "ghost" `user` from the `web_user` in the request. This has the same attributes as a regular user account, except the `type` is 5
and the user has no password. Hence, they will not be able to log in to the app, but they should be prompted to set a password
and finish setting up their account.
4. Create `pet`s from the `web_pet`s in the request. These are the same as any other pet in the database.
5. Create the appropriate `user_pet` relationships. These are the same as any other `user_pet` relationship in the database.
6. Create the `request` in the database, unless the DIY option was selected. This is the same as any other request in the database.

### HTTP Request
`POST /web_request/:webrequestid`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
stripe_token | string? | Optional. Stripe token to charge if this was a paid request.
