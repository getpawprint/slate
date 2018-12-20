# Postcards
Postcards are sent on the cadence specified in reminder_cadence.
Postcards are also batched so that no client receives postcards less than 2 weeks (default) apart.

## Get pending postcards for approval

> Response example

```json
[
    {
        "id": 2,
        "first_name": "Test",
        "last_name": "User",
        "street_address": "123 Main Street",
        "street_address2": null,
        "city": "Beverly Hills",
        "state": "CA",
        "zip": "90210",
        "country": null,
        "reminders": [
            {
                "id": 1849713,
                "text": "Ein's Rabies 3 years is due on 01/03/2019"
            },
            {
                "id": 1849714,
                "text": "Ein's Bordetella Oral is due on 01/02/2019"
            },
            {
                "id": 1849715,
                "text": "Ein's DA2PPC is due on 01/02/2019"
            }
        ],
        "created_at": "2018-12-20T04:38:45.330Z"
    }
]
```

Vet API. Gets all pending postcards for approval, from newest to oldest.

### HTTP Request
`GET /vet/postcard/queue`

### GET parameters
Parameter | Type | Description
--------- | ---- | -----------
show_all | bool? | Whether or not to return all (approved, rejected and pending) postcards. Only pending postcards are returned by default.
dir | string? | Sort order for date. `asc` or `desc`. Defaults to `desc`.

## Approve or reject a postcard
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

Vet API. Approves or rejects a postcard. Approval will trigger the postcard to be mailed.
Rejection will prevent the postcard from showing up again, unless the queue endpoint was called with `show_all=true`.

### HTTP Request
`POST /vet/postcard/queue/:postcard_id`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
approved | bool | Whether or not to send the postcard.
