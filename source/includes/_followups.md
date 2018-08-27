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
