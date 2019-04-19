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
    "pending-records": "1",
    "review": "1",
    "complete": "1",
    "cancelled": 0
  },
  "intake": {
    "initializing": 0,
    "pending-records": "1",
    "review": "1",
    "complete": 0,
    "cancelled": 0
  }
}
```

Gets the status and count for each type of admin portal item.

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
        "admin": 1
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
        "admin": 1
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
        "admin": 1
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
        "admin": 1
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
sort_by | string? | Either `created_at:asc`, `created_at:desc`, `updated_at:asc` or `updated_at:desc`. Default field is `updated_at:desc`.
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er"}`. All channels support filtering by `user_name`, `pet_name`, `status` and `place_name`. Record and appointment requests also support `admin_name`.

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
  "appointment": {
    "date": "2019-04-30",
    "time": "19:35",
    "timezone": "America/Los Angeles",
    "place_id": 46109
  },
  "claim": {
    "id": 10,
    "insurance_name": "Pawprint Farm"
  }
}
```

Gets an individual request's details. Depending on the channel that the app came in, `partner`, `appointment` and `claim` may be null.

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
sort_by | string? | Either `created_at:asc`, `created_at:desc`, `updated_at:asc` or `updated_at:desc`. Default field is `updated_at:desc`.
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
      "status": "pending-records",
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
sort_by | string? | Either `created_at:asc`, `created_at:desc`, `updated_at:asc` or `updated_at:desc`. Default field is `updated_at:desc`.
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er"}`. All channels support filtering by `user_name`, `pet_name`, `status` and `place_name`. Claims also supports `insurance_name`.

## Get an insurance claim

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
  "status": "pending-records",
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
`GET /admin/v2/claims/:claim_id`

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
      "status": "pending-records",
      "created_at": "2019-04-17T23:58:12.932Z",
      "updated_at": "2019-04-17T23:58:19.764Z",
      "appointment": {
        "date": "2019-04-30",
        "time": "19:35",
        "timezone": null,
        "place_id": 46109,
        "place_name": "Megavet of Lakeshire"
      }
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
sort_by | string? | Either `created_at:asc`, `created_at:desc`, `updated_at:asc` or `updated_at:desc`. Default field is `updated_at:desc`.
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er"}`. All channels support filtering by `user_name`, `pet_name`, `status` and `place_name`. Intake also supports `partner_name`.

## Get an intake form

```json
{
  "id": 93,
  "status": "pending-records",
  "user_id": 70420,
  "user_full_name": "User, Test",
  "pet_id": 104308,
  "pet_name": "Spot",
  "partner_id": 1468,
  "partner_name": "Megavet Co.",
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
      "place_name": "Peanut Butter Veterinary Clinic"
    },
    {
      "id": 45179,
      "status": "pending vet",
      "place_name": "Jellystone Animal Hospital"
    }
  ]
}
```

Gets an individual intake form.

### HTTP Request
`GET /admin/v2/intake/:intake_id`
