---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - json

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - activation
  - activation_v2
  - machine_learning
  - medication
  - payments
  - places
  - reminders_v2
  - requests_v1
  - requests_v2
  - sharing

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
