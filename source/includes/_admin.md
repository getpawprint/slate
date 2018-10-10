# Admin portal
APIs that are used by the admin portal. You can find everything in
pawprint-backend/client/admin/app/common/services/utils.srv.js.

## Get list of admins

> Response example

```json
[
    {
        "id": 1,
        "name": "John Lennon"
    },
    {
        "id": 2,
        "name": "Paul McCartney"
    },
    {
        "id": 3,
        "name": "George Harrison"
    },
    {
        "id": 4,
        "name": "Ringo Starr"
    }
]
```

Gets the list of all admins, which are defined as having `"user".type=0`.

### HTTP Request
`GET /admin/admins`

## Get request counts by status

> Response example

```json
{
    "new": 174,
    "pending user": 0,
    "pending vet": 1,
    "received": 0,
    "complete": 7,
    "cancelled": 63,
    "pending": null,
    "total": "268",
    "pending_all": null
}
```

Gets request counts by status - `new`, `pending user`, `pending vet`, etc.

### HTTP Request
`GET /admin/requests/counts`

## Get list of requests

> Response example

```json
[
    {
        "id": 17237,
        "user_id": 1,
        "place_id": 1,
        "pet_id": 10,
        "user_full_name": "Dong, Emily",
        "firstName": "Emily",
        "lastName": "Dong",
        "admin_name": null,
        "pet_name": "Bowser",
        "place_name": "Irving Pet Hospital",
        "status": "new",
        "created_at": "2017-03-17T02:00:30.935Z",
        "stripe_charge_id": null,
        "updated_at": "2017-03-17T02:00:31.572Z",
        "admin": null,
        "code_type": null,
        "type_name": "Paid Full History",
        "type_display": "Full",
        "type_priority": 1,
        "type_description": "User paid for full history plus rush service",
        "request_type": "Records"
    },
    {
        "id": 17241,
        "user_id": 1,
        "place_id": 7881,
        "pet_id": 10,
        "user_full_name": "Dong, Emily",
        "firstName": "Emily",
        "lastName": "Dong",
        "admin_name": null,
        "pet_name": "Bowser",
        "place_name": "North 10th Street Animal Hospital",
        "status": "new",
        "created_at": "2017-03-17T02:22:35.875Z",
        "stripe_charge_id": "ch_19yAk9EBVec3KZrs0NBW60L3",
        "updated_at": "2017-03-17T02:22:36.333Z",
        "admin": null,
        "code_type": null,
        "type_name": "Paid Rush Service",
        "type_display": "Rush",
        "type_priority": 1,
        "type_description": "User paid for rush service",
        "request_type": "Records"
    },
    {
        "id": 17748,
        "user_id": 20,
        "place_id": 1,
        "pet_id": 142,
        "user_full_name": "HatsS, Emma",
        "firstName": "Emma",
        "lastName": "HatsS",
        "admin_name": null,
        "pet_name": "Test",
        "place_name": "Irving Pet Hospital",
        "status": "new",
        "created_at": "2017-04-07T22:13:07.026Z",
        "stripe_charge_id": null,
        "updated_at": "2017-04-07T22:13:12.501Z",
        "admin": 226,
        "code_type": null,
        "type_name": "Basic Record Retrieval",
        "type_display": null,
        "type_priority": 10,
        "type_description": "No add-ons purchased",
        "request_type": "Records"
    }
]
```

Gets a list of requests. This API is NOT PAGINATED so be careful about calling with statuses that have
a lot of requests, like "complete".

### HTTP Request
`GET /admin/requests`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
request.status | string? | Filters by status, e.g. "new", "pending vet", "received", etc.

## Get an individual request

> Response example

```json
{
    "id": 3413,
    "user_id": 1,
    "place_id": 1,
    "pet_id": 1,
    "status": "complete",
    "created_at": "2015-11-25T01:12:21.343Z",
    "updated_at": "2017-03-27T22:48:39.615Z",
    "checkout_type": 1,
    "checkout_notes": null,
    "cost": 0,
    "consent": "https://pawprint-request-consent.s3.amazonaws.com/1-3413-1448413942761.pdf",
    "admin": 226,
    "stripe_charge_id": null,
    "note": null,
    "code_type": null,
    "code_notes": null,
    "code_description": null,
    "code": null,
    "type_name": "Basic Record Retrieval",
    "type_display": null,
    "type_priority": 10,
    "type_description": "No add-ons purchased"
}
```

Gets an individual request by `request.id`.

### HTTP Request
`GET /admin/requests/:requestid`

## Update request

> Request example

```json
{
    "status": "complete",
    "admin": 3
}
```

> Response example

```json
{
    "id": 3413,
    "user_id": 1,
    "place_id": 1,
    "pet_id": 1,
    "status": "complete",
    "created_at": "2015-11-25T01:12:21.343Z",
    "updated_at": "2017-03-27T22:48:39.615Z",
    "checkout_type": 1,
    "checkout_notes": null,
    "cost": 0,
    "consent": "https://pawprint-request-consent.s3.amazonaws.com/1-3413-1448413942761.pdf",
    "admin": 3,
    "stripe_charge_id": null,
    "note": null,
    "code_type": null,
    "code_notes": null,
    "code_description": null,
    "code": null,
    "type_name": "Basic Record Retrieval",
    "type_display": null,
    "type_priority": 10,
    "type_description": "No add-ons purchased"
}
```

Updates an individual request by patching the `request` row with the properties specified in the PUT body.

### HTTP Request
`PUT /admin/requests/:requestid`

## Notify user of request completion

> Request example

```json
{
    "user_id": 1,
    "pet_id": 20,
    "request_id": 3314,
    "place_id": 27015,
}
```

> Response example

```json
(none)
```

Sends a notification to the user that their request is complete. The notification is
sent via push, email and/or SMS based on the user's preferences, which are set in the mobile app.

### HTTP Request
`POST /admin/notify/request`

### POST body parameters
Parameter | Type | Description
--------- | ---- | -----------
user_id | int | ID of the user to notify.
request_id | int | ID of the request.
pet_id | int | ID of the pet in the request.
place_id | int | ID of the place in the request.

## Get/Update any user

> Request example

```json
{
    "firstName": "Russell",
    "lastName": "Wilson"
}
```

> Response example

```json
(newly updated user row)
```

Gets ANY user (if called with GET),
or updates ANY user by patching the `user` row with the properties specified in the PUT body.

### HTTP Request
`GET /admin/users/:userid`

`PUT /admin/users/:userid`

## Get/Update any pet

> Request example

```json
{
    "name": "Chewy"
}
```

> Response example

```json
(newly updated pet row)
```

Gets ANY pet (if called with GET),
or updates ANY pet by patching the `pet` row with the properties specified in the PUT body.

### HTTP Request
`GET /admin/pets/:petid`

`PUT /admin/pets/:petid`

## Get/Update any place

> Request example

```json
{
    "address": "123 Main St, Bellevue, WA 98004"
}
```

> Response example

```json
(newly updated place row)
```

Gets ANY vet/place (if called with GET),
or updates ANY vet/place by patching the `place` row with the properties specified in the PUT body.

### HTTP Request
`GET /admin/places/:petid`

`PUT /admin/places/:petid`

## Get contact history for a request

> Response example

```json
[
    {
        "id": 43189,
        "adminUser_id": 1,
        "type": "Email",
        "notes": "Template email sent",
        "request_id": 17773,
        "contact_id": null,
        "created_at": "2017-12-31T08:45:48.986Z",
        "updated_at": "2017-12-31T08:45:48.987Z",
        "appointment_request_id": null
    }
]
```

Gets contact history for a request.

### HTTP Request
`GET /admin/requests/:requestid/logs`

## Add to contact history for a request

> Response example

```json
{
    "adminUser_id": 1,
    "notes": "Records were received and uploaded",
    "type": "Records received",
    "timestamp": "2017-12-31T08:45:48.986Z"
}
```

Adds a new contact history log item to the request.

### HTTP Request
`POST /admin/requests/:requestid/logs`

### POST body parameters
Parameter | Type | Description
--------- | ---- | -----------
adminUser_id | int? | Admin's user ID who is making the post, otherwise the currently logged in user.
notes | string | Log entry content - freeform string.
type | string | Category of communication - freeform string.
timestamp | datetime | When the addition was made.

## Update contact history log entry

> Request example

```json
{
    "note": "Amended message content"
}
```

> Response example

```json
(newly updated contactHistory row)
```

Updates an individual request by patching the `contactHistory` row with the properties specified in the PUT body.

### HTTP Request
`PUT /admin/requests/:requestid/logs/:logid`

## Delete contact history log entry

> Request example

```json
(none)
```

> Response example

```json
(none)
```

Deletes a contact history log entry.

### HTTP Request
`DELETE /admin/requests/:requestid/logs/:logid`

## Get all contact history regarding a certain user/pet/place

> Response example

```json
[
    {
        "request_id": 17785,
        "notes": "Template email sent",
        "type": "Email",
        "adminFirstName": "Emily",
        "adminId": 1,
        "created_at": "2017-12-31T08:46:41.069Z",
        "clientFirstName": "Test",
        "clientLastName": "User",
        "clientId": 36486,
        "petName": "Rover",
        "petId": 65388,
        "placeName": "Test place",
        "placeId": 8443
    }
]
```

Gets all contact history regarding a certain user/pet/place. DO NOT CALL WITHOUT QUERY PARAMETERS or
else the response will be really long.

### HTTP Request
`GET /admin/logs`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
request.user_id | int? | Filters by user
request.pet_id | int? | Filters by pet
request.place_id | int? | Filters by place

## Get PDF records from a request

> Response example

```json
[
    {
        "id": 14835,
        "link": "https://pawprint-request-pdf.s3.amazonaws.com/17773-123456789.pdf",
        "request_id": 17773,
        "created_at": "2017-04-05T22:56:12.257Z",
        "updated_at": "2017-04-05T22:56:12.257Z",
        "description": "undefined",
        "start_date": null,
        "end_date": "2017-04-05T07:00:00.000Z"
    }
]
```

Gets all PDF records that were uploaded to a request.

### HTTP Request
`GET /admin/requests/:requestid/pdfs`

## Upload PDF record to a request

> Request example

```json
(none)
```

> Response example

```json
(none)
```

Uploads a PDF to our Amazon S3 pdfBucket and saves the link in the `pdfRecord` table.

### HTTP Request
`POST /admin/requests/:requestid/pdfs`

## Delete PDF record to a request

> Response example

```json
(none)
```

Soft-deletes the link to the PDF record from the `pdfRecord`; however, the actual PDF will remain in Amazon S3.

### HTTP Request
`DELETE /admin/requests/:requestid/pdfs/:pdfid`

## Update/regenerate consent form

> Request example

```json
(none)
```

> Response example

```json
(none)
```

Regenerates the consent PDF attached to the request, in case the request had something changed.

### HTTP Request
`POST /admin/requests/:requestid/consents`

## Send request to vet via email

> Request example

```json
(none)
```

> Response example

```json
(none)
```

Emails a message and consent form to the vet associated with the request. **THIS EMAIL WILL APPEAR
TO BE FROM CASSIE MOSS**. This only works if the vet has an email address in the place table.

### HTTP Request
`POST /admin/requests/:requestid/emails`

## Send request to vet via fax

> Request example

```json
(none)
```

> Response example

```json
(none)
```

Faxes a message and consent form to the vet associated with the request. This only works if the vet has a fax number in the place table.

### HTTP Request
`POST /admin/requests/:requestid/faxes`

## Get contacts for a vet

> Response example

```json
[
    {
        "id": 1214,
        "name": "Olivia",
        "title": null,
        "email": null,
        "phone": null,
        "place_id": 1,
        "created_at": "2016-05-09T14:32:23.371Z",
        "updated_at": "2016-05-09T14:32:23.371Z"
    },
    {
        "id": 2823,
        "name": "Katheryn",
        "title": "",
        "email": "",
        "phone": "",
        "place_id": 1,
        "created_at": "2016-10-10T22:28:13.095Z",
        "updated_at": "2016-10-10T22:28:13.095Z"
    }
]
```

Gets the contact information for people who work at a particular place.

### HTTP Request
`GET /admin/places/:placeid/contacts`

## Add contact at a vet

> Request example

```json
{
    "name": "Morgan Lee",
    "title": "Manager",
    "email": "example@getpawprint.com,
    "phone": "19145555555"
}
```

> Response example

```json
(none)
```

Adds contact information about someone who works at a particular place.

### HTTP Request
`POST /admin/places/:placeid/contacts`

### POST body parameters
Parameter | Type | Description
--------- | ---- | -----------
name | string | The person's name.
title | string? | The person's title.
email | string? | The person's email address.
phone | string? | The person's phone number.

## Update contact

> Request example

```json
{
    "name": "Morgan Smith",
}
```

> Response example

```json
(newly updated contact row)
```

Updates contact information about someone who works at a particular place.

### HTTP Request
`PUT /admin/places/:placeid/contacts/:contactid`

## Delete contact

> Response example

```json
(none)
```

Deletes a contact from a place.

### HTTP Request
`DELETE /admin/places/:placeid/contacts/:contactid`
