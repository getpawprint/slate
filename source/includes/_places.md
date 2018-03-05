# Places
Places are backed by the `place` table and are referenced by a lot of other
tables, such as `request`, `business_pet`, and `event`.

## Add new place
> Request examples

```json
{
	"name": "Super Test Vet",
	"address": "621 Escondido Rd, Stanford, CA 94305",
	"email": "supertestvet@getpawprint.com",
	"website": "http://example.com",
	"placesid": "ChIJ8d3CFNe6j4AR21tyt88xx2g",
	"fax": "15555551234",
	"phone": "15555556789"
}
```

> Response example

```json
{
    "id": 11440,
    "name": "Super Test Vet",
    "website": "http://example.com",
    "contactMethod": null,
    "created_at": "2018-03-05T04:38:37.090Z",
    "updated_at": "2018-03-05T04:38:37.090Z",
    "placesid": "ChIJ8d3CFNe6j4AR21tyt88xx2g",
    "address": "621 Escondido Rd, Stanford, CA 94305",
    "fax": "15555551234",
    "email": "supertestvet@getpawprint.com",
    "hours": null,
    "vetstreet_url": null,
    "verified": null,
    "phone": "15555556789",
    "type": null,
    "pawprint_note": null,
    "twilio_phone": null,
    "appointment_email_opt_out": false
}
```

Adds a new place to the `place` table and returns the entire row. If the place existed already
(based on matching name & address or matching Google Places ID), the existing place is returned
and no new place is added.

### HTTP Request
`POST /user/places`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
name | String? | The place's name
address | String? | The place's physical location address
email | String? | The place's email address
website | String? | The place's web site address
placesid | String? | A Google Place ID
fax | String? | The place's fax number
phone | String? | The place's phone number
type | String | "vet" if it's a vet (so far the only values in the database are "vet", blank or "unknown"



