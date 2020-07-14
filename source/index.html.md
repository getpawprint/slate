---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - admin
  - admin_v2
  - appointment_requests
  - events_v2
  - insurance
  - intake
  - vet_intake_portal
  - vet_portal_communications
  - configuration
  - machine_learning
  - medication
  - payments
  - places
  - places_v3
  - requests_v3
  - sharing
  - user
  - vetstoria
  - legacy_portal

search: true
---
# Templates/examples
Use these as a template by copying and pasting them.

## POST example
> Request example

```json
{
	"user_id": 1,
	"pet_id": 2
	"notes": [ "Hey", "there" ],
	"timestamp": "2018-02-20T21:00:00.000Z"
}
```

> Response example

```json
{
    "id": 340
}
```

Summary of the API and remarks go here.

### HTTP Request
`POST /user/:uriparameter/example/:anotheruriparameter`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
user_id | int | ID from the `user` table.
pet_id | int| ID from the `pet`
notes | array of strings | Notes
timestamp | datetime | February twentieth

## GET example

> Response example

```json
{
    "id": 340
}
```

Gets a user profile by `user.id`.

### HTTP Request
`GET /user/:userid`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
since | datetime | Gets updates made since this time.
$top | int | Limits the number of results.
$skip | int | Results start at this index.
