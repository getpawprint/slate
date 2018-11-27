# Followup surveys
Use these as a template by copying and pasting them.

## Update a followup survey
> Request example

```json
{
	"stars": 4,
	"feedback": "Very clean and professional"
}
```

> Response example

```json
(none)
```

Updates a followup survey. The survey is created by a periodic Luigi task and the link (with
a randomized external ID) is emailed out to users. It can be updated an arbitrary number of times.

### HTTP Request
`PUT /followup/:followup_external_id`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
stars| int? | Number of stars
feedback | string? | Text review string


## Get pending followup surveys for approval

> Response example

```json
[{
	"first_name": "Eric",
	"last_name": "Choi",
	"vet_user_id": 123456,
	"appointments": [
		{
			"pet_name": "Glumpy",
			"title": "Re-check",
			"date": "2018-11-30T12:30"
		},
		{
			"pet_name": "Tobi",
			"title": "Vaccine",
			"date": "2018-11-30T13:30"
		}
	]
}]
```

Vet API. Gets all pending followup surveys for approval, from newest to oldest.

### HTTP Request
`GET /vet/followup`

### GET parameters
Parameter | Type | Description
--------- | ---- | -----------
show_all | bool? | Whether or not to return all (approved, rejected and pending) followup surveys.
dir | string? | Sort order for date. `asc` or `desc`. Defaults to `desc`.

## Approve or reject a followup survey
> Request example

```json
{
	"approved": true
}
```

> Response example

```json
(none)
```

Vet API. Approves or rejects a followup survey. Approval will trigger the email to be sent.
Rejection will prevent the survey from showing up again.

### HTTP Request
`POST /vet/followup/:followup_id

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
approved | bool | Whether or not to send the followup survey.
