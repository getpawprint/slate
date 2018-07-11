# Web requests
Save and retrieve progress for creating a web request

## Create or update user
> Request example

```json
{
	"first_name": "Cecil",
	"last_name": "Harvey",
	"email": "demo@getpawprint.com",
	"phone": "+14255556174",
	"street_address": "123 Main St",
	"street_address2": "Apt 101",
	"city": "Bellevue",
	"state": "WA",
	"country": "United States"
}
```

> Response example

```json
{
	"id": 1
}
```

Returns the web_user ID of the web user, based on email match.
The web_user is either created or updated; which operation is not transparent to the caller.

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

Returns the web_pet ID of the pet, based on name match.
The web_pet is either created or updated; which operation is not transparent to the caller.
The pet's owner must be specified in the URL parameter :webuserid.

### HTTP Request
`POST /web_request/user/:webuserid/pets`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
name | string | Required. Pet's name.
species | string | Required. Pet's species, e.g. "Dog", "Cat", "Rabbit", etc.
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
	"reason": "Need proof of vaccination"
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
web_pet_ids | array of ints | Optional. List of web_pet IDs. The previously stored list is completely overwritten by this list.
place_id | int | Optional. ID from the `place` table.
checkout_type | int | Optional. ID from the `product` table.
reason | string | Optional. User's reason for the records request.

## Get existing request

> Response example

```json
{
	"web_user_id": 1,
	"web_pet_ids": [ 2, 5 ],
	"place_id": 1337,
	"checkout_type": 1,
	"reason": "Need proof of vaccination"
}
```

Gets an existing web request.
Requests are assigned a unique random string which is used as the request identifier.

### HTTP Request
`GET /web_request/:webrequestid`

