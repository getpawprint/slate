# Admin portal V2
Now includes partner information, insurance claims, intake forms and partner requests!

## Get status counts

> Response example

```json
{
  "requests": {
    "initializing": 0,
    "new": 0,
    "verbal requested": 0,
    "verbal complete": 0,
    "pending user": 0,
    "pending vet": "3",
    "complete": "1",
    "complete linked": 0,
    "cancelled": 0
  },
  "appointment_requests": {
    "new": 0,
    "pending user": 0,
    "pending vet": "1",
    "complete": 0,
    "cancelled": 0
  },
  "claims": {
    "initializing": 0,
    "pending records": "1",
    "in review": "1",
    "complete": "1",
    "cancelled": 0
  },
  "intake": {
    "pending user": 0,
    "pending records": "1",
    "in review": "1",
    "complete": 0,
    "cancelled": 0
  }
}
```

Gets the status and count for each type of admin portal item. Each type of admin portal item has its endpoint as well, which returns the appropriate subset of statuses, e.g. `/admin/v2/statuses/requests`, `/admin/v2/statuses/intake`, etc.

### HTTP Request
`GET /admin/v2/statuses`

## Get record requests

> Response example

```json
{
  "data": [
    {
      "request": {
        "id": 45181,
        "user_id": 70420,
        "user_name": "User, Test",
        "pet_id": 104308,
        "pet_name": "Spot",
        "place_id": 46108,
        "place_name": "Rainbow Road Emergency Vet",
        "pawprint_vet_id": null,
        "status": "complete",
        "created_at": "2019-04-18T00:28:38.626Z",
        "updated_at": "2019-04-18T00:28:38.626Z",
        "admin": 1,
        "admin_name": "Emily"
      },
      "product": {
        "slug": "pawprintfarm",
        "title": "Insurance - Pawprint Farm"
      },
      "appointment": null
    },
    {
      "request": {
        "id": 45180,
        "user_id": 70420,
        "user_name": "User, Test",
        "pet_id": 104308,
        "pet_name": "Spot",
        "place_id": 46107,
        "place_name": "Jellystone Animal Hospital",
        "pawprint_vet_id": null,
        "status": "pending vet",
        "created_at": "2019-04-18T00:28:38.616Z",
        "updated_at": "2019-04-18T00:28:38.616Z",
        "admin": 1,
        "admin_name": "Emily"
      },
      "product": {
        "slug": "pawprintfarm",
        "title": "Insurance - Pawprint Farm"
      },
      "appointment": null
    },
    {
      "request": {
        "id": 45179,
        "user_id": 70420,
        "user_name": "User, Test",
        "pet_id": 104308,
        "pet_name": "Spot",
        "place_id": 46107,
        "place_name": "Jellystone Animal Hospital",
        "pawprint_vet_id": null,
        "status": "pending vet",
        "created_at": "2019-04-17T22:27:06.743Z",
        "updated_at": "2019-04-17T22:27:06.743Z",
        "admin": 1,
        "admin_name": "Emily"
      },
      "product": {
        "slug": "megavet",
        "title": "Intake - Megavet record request"
      },
      "appointment": {
        "date": "2019-04-30",
        "time": "19:35",
        "timezone": null,
        "place_id": 46109
      }
    },
    {
      "request": {
        "id": 45178,
        "user_id": 70420,
        "user_name": "User, Test",
        "pet_id": 104308,
        "pet_name": "Spot",
        "place_id": 46106,
        "place_name": "Peanut Butter Veterinary Clinic",
        "pawprint_vet_id": null,
        "status": "pending vet",
        "created_at": "2019-04-17T22:27:06.725Z",
        "updated_at": "2019-04-17T22:27:06.725Z",
        "admin": null,
        "admin_name": null
      },
      "product": {
        "slug": "megavet",
        "title": "Intake - Megavet record request"
      },
      "appointment": {
        "date": "2019-04-30",
        "time": "19:35",
        "timezone": null,
        "place_id": 46109
      }
    }
  ],
  "count": 4
}
```

Gets a list of record request summaries.

### HTTP Request
`GET /admin/v2/requests`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | Either `{"created_at":"asc"}`, `{"created_at":"desc"}`, `{"updated_at":"asc"}`, `{"updated_at":"desc"}`, `{"appointment_date":"asc"}` or `{"appointment_date":"desc"}`. Default field is `{"updated_at":"desc"}`.
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er"}`. All channels support filtering by `user_name`, `pet_name`, `status` and `place_name`. Record requests also supports `id`, `user_id`, `pet_id` and `place_id`/`vet_id`.

## Get a record request

> Response example

```json
{
  "request": {
    "id": 45179,
    "user_id": 70420,
    "pet_id": 104308,
    "place_id": 46107,
    "pawprint_vet_id": null,
    "pawprint_vet_place_id": null,
    "status": "pending vet",
    "created_at": "2019-04-17T22:27:06.743Z",
    "updated_at": "2019-04-17T22:27:06.743Z",
    "checkout_notes": null,
    "consent": "https://www.getpawprint.com/consent.png",
    "admin": 1,
    "note": "He bites hands occasionally"
  },
  "order": {
    "id": 1020,
    "stripe_charge_id": "stripecharge0010",
    "promocode": "flat5",
    "charge_amount": 1998
  },
  "product": {
    "slug": "megavet",
    "title": "Intake - Megavet record request",
    "description": "Full medical records for Megavet intake"
  },
  "partner": {
    "id": 1468,
    "type": "vet",
    "name": "Megavet Co."
  },
  "claims": [{
    "id": 10,
    "insurance_name": "Pawprint Farm"
  }],
  "links": [
    {
      "intake": {
        "id": 41,
        "status": "in review"
      },
      "appointment": {
        "place_id": 27015,
        "place_name": "Test Animal Clinic",
        "date": "2019-05-02",
        "time": "10:00 am",
        "timezone": null
      }
    },
    {
      "appointment": {
        "place_id": 1,
        "place_name": "Irving Pet Hospital",
        "date": "2019-05-02",
        "time": "10:30 am",
        "timezone": null
      }
    }
  ]
}
```

Gets an individual request's details. Depending on the channel that the request came from, `partner`, `links` and `claim` may be null.

### HTTP Request
`GET /admin/v2/requests/:request_id`

## Get appointment requests

> Response example

```json
{
  "data": [
    {
      "id": 807,
      "user_id": 70420,
      "place_id": 46106,
      "pet_id": 104308,
      "user_full_name": "Test User",
      "pawprint_vet_id": null,
      "pawprint_vet_place_id": null,
      "admin_name": "Emily",
      "pet_name": "Spot",
      "place_name": "Peanut Butter Veterinary Clinic",
      "status": "pending vet",
      "created_at": "2019-04-18T01:17:39.508Z",
      "updated_at": "2019-04-18T01:17:39.508Z",
      "admin": 1
    }
  ],
  "count": 1
}
```

Gets a list of appointment request summaries.

### HTTP Request
`GET /admin/v2/appointment_requests`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | Either `{"created_at":"asc"}`, `{"created_at":"desc"}`, `{"updated_at":"asc"}` or `{"updated_at":"desc"}`. Default field is `{"updated_at":"desc"}`.
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er"}`. All channels support filtering by `user_name`, `pet_name`, `status` and `place_name`. Record and appointment requests also support `admin_name`.

## Get an appointment request

> Response example

```json
{
    "id": 807,
    "user_id": 70420,
    "user_name": "Test User",
    "email": "testuser@getpawprint.com",
    "phone": "425-555-5555",
    "pet_id": 104308,
    "pet_name": "Spot",
    "pet_species": "Dog",
    "pet_breed": "Staffordshire Terrier",
    "place_id": 46106,
    "status": "pending vet",
    "created_at": "2019-04-18T01:17:39.508Z",
    "updated_at": "2019-04-18T01:17:39.508Z",
    "description": "Wellness exam",
    "notes": "Bites hands occasionally",
    "time": [
        "2019-04-22T08:30:00-07:00",
        "2019-04-22T09:00:00-07:00",
        "2019-04-22T20:00:00-07:00",
        "2019-04-22T19:30:00-07:00",
        "2019-04-22T19:00:00-07:00",
        "2019-04-22T18:30:00-07:00",
        "2019-04-22T18:00:00-07:00",
        "2019-04-22T17:30:00-07:00",
        "2019-04-22T17:00:00-07:00",
        "2019-04-22T16:30:00-07:00",
        "2019-04-22T16:00:00-07:00",
        "2019-04-22T09:30:00-07:00"
    ],
    "accepted_time": "2019-04-22T15:30:00Z",
    "admin_id": 1,
    "admin_name": "Emily"
}
```

Gets a single appointment request's details.

### HTTP Request
`GET /admin/v2/appointment_requests/:appointment_request_id`

## Get insurance claims

```json
{
  "data": [
    {
      "id": 327,
      "user_id": 70420,
      "place_id": 46106,
      "pet_id": 104308,
      "user_full_name": "User, Test",
      "pet_name": "Spot",
      "place_name": "Peanut Butter Veterinary Clinic",
      "insurance_name": "Pawprint Farm",
      "status": "pending records",
      "created_at": "2019-04-18T00:28:38.611Z",
      "updated_at": "2019-04-18T00:28:38.611Z"
    },
    {
      "id": 326,
      "user_id": 37683,
      "place_id": 27015,
      "pet_id": 103843,
      "user_full_name": "Choi, Eric",
      "pet_name": "Glumpy",
      "place_name": "Pawprint, Inc.",
      "insurance_name": "Nationwide",
      "status": "complete",
      "created_at": "2019-04-16T17:00:02.552Z",
      "updated_at": "2019-04-16T17:00:02.552Z"
    }
  ],
  "count": 2
}
```

Gets a list of insurance claims.

### HTTP Request
`GET /admin/v2/claims`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | Either `{"created_at":"asc"}`, `{"created_at":"desc"}`, `{"updated_at":"asc"}` or `{"updated_at":"desc"}`. Default field is `{"updated_at":"desc"}`.
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er"}`. All channels support filtering by `user_name`, `pet_name`, `status` and `place_name`. Claims also supports `insurance_name`.

<!-- ## Get an insurance claim

```json
{
  "id": 327,
  "user_id": 70420,
  "pet_id": 104308,
  "place_id": 46106,
  "pet_insurance_id": 2847,
  "visit_type": "wellness",
  "symptoms_start_date": "2018-11-01",
  "diagnosis": "Gastroenteritis",
  "is_new_condition": true,
  "was_claimed_before": false,
  "invoice_date": "2018-11-05",
  "invoice_total": "259.33",
  "treatment_start_date": null,
  "treatment_end_date": null,
  "signature": "https://www.getpawprint.com/signature.png",
  "should_create_request": true,
  "status": "pending records",
  "link": "https://www.getpawprint.com/claim.pdf",
  "additional_insurance_name": null,
  "additional_insurance_cancel_date": null,
  "symptoms": [
    "Diarrhea",
    "Vomiting",
    "Lack of appetite"
  ],
  "files": [
    {
      "link": "https://www.getpawprint.com",
      "name": "My uploaded file"
    }
  ],
  "requests": [
    {
      "id": 45180,
      "status": "pending vet",
      "place_name": "Jellystone Animal Hospital"
    },
    {
      "id": 45181,
      "status": "complete",
      "place_name": "Rainbow Road Emergency Vet"
    }
  ]
}
```

Gets a single insurance claim's details.

### HTTP Request
`GET /admin/v2/claims/:claim_id` -->

## Get intake forms

```json
{
  "data": [
    {
      "id": 93,
      "user_id": 70420,
      "user_full_name": "User, Test",
      "pet_id": 104308,
      "pet_name": "Spot",
      "partner_id": 1468,
      "partner_name": "Megavet Co.",
      "status": "pending records",
      "created_at": "2019-04-17T23:58:12.932Z",
      "updated_at": "2019-04-17T23:58:19.764Z",
      "appointment": {
        "date": "2019-04-30",
        "time": "19:35",
        "timezone": null,
        "place_id": 46109,
        "place_name": "Megavet of Lakeshire"
      },
      "admin_id": 1,
      "admin_name": "Emily"
    }
  ],
  "count": 1
}
```

Gets a list of intake forms.

### HTTP Request
`GET /admin/v2/intake`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | JSON object, where the key is the sort field and the value is the sort direction (`asc` or `desc`). Sort fields are `created_at`, `updated_at`, and `appointment_time`. Default is `{"updated_at":"desc"}`.
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er"}`. All channels support filtering by `user_name`, `pet_name`, `status` and `place_name`. Intake also supports `partner_name`.

## Get an intake form

```json
{
  "id": 93,
  "status": "pending records",
  "user_id": 70420,
  "user_full_name": "User, Test",
  "pet_id": 104308,
  "pet_name": "Spot",
  "partner_id": 1468,
  "partner_name": "Megavet Co.",
  "link": "https://pawprint-summary.s3.aws.amazon.com/YJ1300cllreiancFl.pdf",
  "review_notes": "* Missed a year of rabies",
  "created_at": "2019-04-17T23:58:12.932Z",
  "updated_at": "2019-04-17T23:58:19.764Z",
  "appointment": {
    "date": "2019-04-30",
    "time": "19:35",
    "timezone": null,
    "place_id": 46109,
    "place_name": "Megavet of Lakeshire"
  },
  "requests": [
    {
      "id": 45178,
      "status": "pending vet",
      "place_id": 547,
      "place_name": "Peanut Butter Veterinary Clinic",
      "place_phone": "(206) 555-5555"
    },
    {
      "id": 45179,
      "status": "pending vet",
      "place_id": 14501,
      "place_name": "Jellystone Animal Hospital",
      "place_phone": "(425) 555-5555"
    }
  ],
  "admin_id": 1,
  "admin_name": "Emily"
}
```

Gets an individual intake form.

### HTTP Request
`GET /admin/v2/intake/:intake_id`

## Update intake form (admin)
> Request example

```json
{
  "status": "complete",
  "review_notes": "Bites hands occasionally",
  "empty_reason": "No records",
  "admin_id": 1
}
```

> Response example

```json
(none)
```

Updates an intake form. Only the admin, review notes, empty reason or status can be updated. Setting the status to `complete` will cause the Pawprint Summary to be automatically generated and the URL stored in `intake.link`.

### HTTP Request
`PUT /admin/v2/intake/:intake_id`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
status | string? | One of "in review", "complete" or "cancelled".
empty_reason | string? | Freeform string explaining why the intake has been completed or cancelled with 0 records attached.
review_notes | string? | Important observations that came up during the admin review.
admin_id | int? | Assigns an admin to handle this case.

## Regenerate Pawprint Summary
> Request example

```json
(none)
```

> Response example

```json
{ "link": "https://pawprint-summary.s3.aws.amazon.com/YJ1300cllreiancFl.pdf" }
```

Regenerates the Pawprint Summary PDF and stores the URL in `intake.link`.

### HTTP Request
`POST /admin/v2/intake/:intake_id/summary`

## Get intakes for pet

```json
[
  {
    "id": 21,
    "user_id": 37683,
    "user_full_name": "Choi, Eric",
    "pet_id": 68795,
    "pet_name": "Glumpy",
    "partner_id": 5,
    "partner_name": "Pawprint Farm",
    "status": "complete",
    "created_at": "2019-04-25T00:30:34.941Z",
    "updated_at": "2019-04-25T00:30:34.941Z",
    "appointment": {
      "date": "2019-04-24",
      "time": "1:30 pm",
      "timezone": null,
      "place_id": 27015,
      "place_name": "Test Animal Clinic"
    }
  }
]
```

Gets all intakes associated with a pet.

### HTTP Request
`GET /admin/pets/:petid/intake`

<!-- ## Get insurance claims for pet

```json
[
  {
    "id": 31,
    "user_id": 1000000003,
    "place_id": 27015,
    "pet_id": 1000000033,
    "user_full_name": "Zo, Eli",
    "pet_name": "Dates",
    "place_name": "Test Animal Clinic",
    "insurance_name": "ASPCA Pet Health Insurance",
    "status": "pending records",
    "created_at": "2019-04-09T15:16:23.305Z",
    "updated_at": "2019-04-09T15:16:23.305Z"
  }
]
```

Gets all insurance claims associated with a pet.

### HTTP Request
`GET /admin/pets/:petid/claims` -->

## Get appointment requests for pet

```json
[
  {
    "id": 314,
    "user_id": 159,
    "place_id": 2653,
    "pet_id": 58979,
    "user_full_name": "John Smith",
    "pawprint_vet_id": null,
    "pawprint_vet_place_id": null,
    "admin_name": null,
    "pet_name": "Glumpy",
    "place_name": "Laurel Pet Hospital",
    "status": "complete",
    "created_at": "2018-04-20T17:57:50.083Z",
    "updated_at": "2018-04-20T19:51:00.000Z",
    "admin": null
  }
]
```

Gets all appointment requests associated with a pet.

### HTTP Request
`GET /admin/pets/:petid/appointment_request`

## Get places

```json
{
    "data": [
        {
            "id": 14501,
            "name": "Banfield of Bellevue Overlake",
            "address": "15445 NE 24th St, Bellevue, WA 98007",
            "website": "http://www.banfield.com/our-hospitals/hospital-locations/location-pages/BEO",
            "phone": "(425) 746-2865",
            "fax": "4257474883",
            "email": "na",
            "contactMethod": null,
            "verified": true,
            "pawprint_note": null,
            "created_at": "2018-05-01T20:51:36.252Z",
            "updated_at": "2018-10-26T21:56:04.922Z"
        },
        {
            "id": 547,
            "name": "Banfield of Bellevue",
            "address": "100 108th Ave NE, Bellevue, WA 98004",
            "website": "http://www.banfield.com/our-hospitals/hospital-locations/location-pages/BLV",
            "phone": "(425) 454-7023",
            "fax": "4254548967",
            "email": null,
            "contactMethod": "Call",
            "verified": true,
            "pawprint_note": null,
            "created_at": "2015-05-26T16:41:11.921Z",
            "updated_at": "2018-10-26T21:56:04.922Z"
        }
    ],
    "count": "2"
}
```

Gets a list of Pawprint places only (does not query Google Places), optionally with sorting and filtering.

Supported filters are:

- `name`
- `address` - matches freeform strings in the `place.address` field only. Address strings typically contain a zip code and a city name.
- `phone` - Numbers only. When searching the database, phone numbers like "(425) 753-1234" are normalized to "4257536174", so filtering with "42575" will still produce a match.

### HTTP Request
`GET /admin/v2/places`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | JSON object, where the key is the sort field and the value is the sort direction (`asc` or `desc`). Sort fields are `created_at`, `updated_at`, and `id`. Default is `{"updated_at":"desc"}`.
filter | string? | Filter fields separated by commas, e.g. `filter={"name":"banf","address":"98004","phone":"425"}`.

## Add intake log entry
> Request example

```json
{
	"type": "Automated reminder",
	"notes": "Sent an automated reminder"
}
```

> Response example

```json
(none)
```

Adds an entry to the `intake_log` table.

### HTTP Request
`POST /admin/v2/intake/:intake_id/logs`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
type | string | Note type (Ideally a limited set)
notes | string | Note body

## Get intake logs

> Response example

```json
[
  {
    "admin_id": 0,
    "admin_name": "System",
    "type": "Automated event",
    "notes": "Automatically advanced to 'in review'",
    "created_at": "2019-04-21T00:35:42Z"
  },
  {
    "admin_id": 1,
    "admin_name": "Emily Dong",
    "type": "Appointment delayed",
    "notes": "Vet notified me of something",
    "created_at": "2018-02-01T00:35:42Z"
  }
]
```

Gets all the log entries for an intake.

### HTTP Request
`GET /admin/v2/intake/:intake_id/logs`
