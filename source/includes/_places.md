# Places
Places are backed by the `place` table and are referenced by a lot of other
tables, such as `request`, `business_pet`, and `event`.

## Add new place
> Request example

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

## Places search

> Response example

```json
[
    {
        "id": 609,
        "placesid": "ChIJ08cxkN1oYogRJfpZbg5DPHM",
        "googlePlaceID": "ChIJ08cxkN1oYogRJfpZbg5DPHM",
        "name": "Trusty Vet - Affordable Pet Care - Research Park",
        "practiceName": "Trusty Vet - Affordable Pet Care - Research Park",
        "address": "6945 Highway 72 West, Huntsville, AL 35806undefined",
        "phone": "2564898387",
        "fax": "2564898353",
        "website": "http://www.trustyvet.com/",
        "unformattedPhone": "2564898387",
        "twilio_phone": "2564898387",
        "custom_message": null,
        "canFormatPhone": true
    },
    {
        "id": 11748,
        "placesid": "ChIJ_____8-9uokRdyo3YP5dbAA",
        "googlePlaceID": "ChIJ_____8-9uokRdyo3YP5dbAA",
        "name": "Affordable Veterinary Services of Virginia",
        "practiceName": "Affordable Veterinary Services of Virginia",
        "address": "5457 Indian River Rd, Virginia Beach, VA 23464",
        "phone": "9077453219",
        "fax": null,
        "website": "http://affordableveterinaryservices.com/",
        "unformattedPhone": "907-745-3219",
        "twilio_phone": null,
        "custom_message": "This vet is walk-in only."
    },
    {
        "id": "ChIJv7y3eRQ-ZIgRIa8WUeIUzwo",
        "placesid": "ChIJv7y3eRQ-ZIgRIa8WUeIUzwo",
        "googlePlaceID": "ChIJv7y3eRQ-ZIgRIa8WUeIUzwo",
        "name": "Affordable Veterinary Care",
        "practiceName": "Affordable Veterinary Care",
        "address": "11314 Lebanon Rd, Mt Juliet, TN 37122",
        "phone": "16157888008",
        "website": "http://www.veterinarianmtjuliet.com/",
        "unformattedPhone": "+1 615-788-8008",
        "canFormatPhone": true
    }
]
```

Searches the `place` table, Google Vets, and Google Businesses. Results are unified
into a single format, then certains fields like phone and address are formatted, and finally,
the results are de-duped by ID, address, phone number, and name,
with a preference for keeping items from the `place` table over Google results.

### HTTP Request
`GET /user/places`

### GET parameters
Parameter | Type | Description
--------- | ---- | -----------
0 | String | Search term

## Create appointment request at a place
> Request example

```json
{
	"email": "echoi@getpawprint.com",
	"phone": "425-555-1234",
	"pet_name": "Pumpkin",
	"pet_species": "Cat",
	"pet_breed": "Domestic shorthair",
	"appointment_type_id": 2,
	"time": ["2017-12-15T08:00:00-06:00"],
	"notes": "Testing",
	"user_name": "Eric Choi"
}
```

> Response example

```json
{

}
```

Creates an appointment request to the given place.

If the place is linked to an onboarded Pawprint vet, an email is automatically sent to the vet
with the appointment request, and the automated email will show up in the request's contact log.
An onboarded Pawprint vet can be associated with multiple places; the join table is `pawprint_vet_place`.

If the place is not linked to an onboarded Pawprint vet, the appointment request will show as a new
request on the admin dashboard, and one of the admins will handle it.

<aside class="notice">
The requested time(s) are displayed in the user's time zone; however, the accepted time will appear in the admin's time zone.
For example, the requested times will appear as US/Eastern, but if the admin page is viewed in San Francisco,
the accepted time will appear as US/Pacific.
</aside>

### HTTP Request
`POST /place/:placeid/appointment`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
user_name | String | The user's name
user_id | int? | The user's ID, if the user is logged in
email | String? | The user's email
phone | String? | The place's phone number
pet_id | int? | An ID from the `pet` table, if the user is logged in
pet_name | String | The pet's name
pet_species | String? | The pet's species, e.g. "cat", "dog"
pet_breed | String? | The pet's breed, e.g. "Pit bull mix"
appointment_type_id | int | Appointment type ID from the `appointment_type` table
time | json | JSON list of requested times
notes | String? | Note to vet

