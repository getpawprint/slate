# Admin portal V2
Now includes partner information, insurance claims, intake forms and partner requests!

## Get record requests

> Response example

```json
[
  {
    "request": {
      "id": 3159,
      "user_id": 100,
      "user_name": "Smith, John",
      "pet_id": 20,
      "pet_name": "Chewie",
      "place_id": 27015,
      "place_name": "Jellystone Animal Hospital",
      "status": "new",
      "created_at": "2019-01-15T13:14:00Z",
      "updated_at": "2019-01-15T13:14:00Z",
      "checkout_notes": "Note to Pawprint",
      "consent": "https://s3.aws.amazon.com/pawprint/pawprint-consent/3159_AxsEJnkstln259.pdf",
      "admin": 37683,
      "note": "Note to the vet"
    },
    "product": {
      "slug": "peanutbutter",
      "title": "Intake - Peanut Butter Veterinary Clinic"
    },
    "appointment": {
      "date": "2019-04-22",
      "time": "16:45",
      "timezone": "America/Los Angeles",
      "place_id": 43919,
      "place_name": "Peanut Butter Veterinary Clinic"
    }
  }
]
```

Gets a list of record request summaries.

### HTTP Request
`GET /admin/v2/requests`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | Sort fields separated by commas, e.g. `sort_by=updated_at:desc,user_name:asc`
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er","place_id":27015}`

## Get a record request

> Response example

```json
{
  "request": {
    "id": 3159,
    "user_id": 100,
    "pet_id": 20,
    "place_id": 27015,
    "status": "new",
    "created_at": "2019-01-15T13:14:00Z",
    "updated_at": "2019-01-15T13:14:00Z",
    "checkout_notes": "Note to Pawprint",
    "consent": "https://s3.aws.amazon.com/pawprint/pawprint-consent/3159_AxsEJnkstln259.pdf",
    "admin": 37683,
    "note": "Note to the vet"
  },
  "order": {
    "id": 1020,
    "stripe_charge_id": "stripecharge0010",
    "promocode": "flat5",
    "charge_amount": 1998
  },
  "product": {
    "slug": "peanutbutter",
    "title": "Intake - Peanut Butter Veterinary Clinic",
    "description": "Full medical records for an intake form"
  },
  "partner": {
    "id": 3,
    "type": "vet",
    "name": "Peanut Butter Veterinary Clinic"
  },
  "appointment": {
    "date": "2019-04-22",
    "time": "16:45",
    "timezone": "America/Los Angeles",
    "place_id": 1
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
[
  {
    "id": 549,
    "user_id": 948,
    "user_full_name": "John Smith",
    "pet_id": 3,
    "pet_name": "Zorro",
    "place_id": 4,
    "place_name": "Jellystone Animal Hospital",
    "pawprint_vet_id": 1,
    "pawprint_vet_place_id": 2,
    "admin_id": 15,
    "admin_name": "Cassie",
    "status": "new",
    "created_at": "2019-01-15T09:15:00Z",
    "updated_at": "2019-01-16T08:30:00Z"
  }
]
```

Gets a list of appointment request summaries.

### HTTP Request
`GET /admin/v2/appointment_requests`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | Sort fields separated by commas, e.g. `sort_by=updated_at:desc,user_name:asc`
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er","place_id":27015}`

## Get an appointment request

> Response example

```json
{
  "id": 549,
  "user_id": 948,
  "user_name": "John Smith",
  "email": "johnsmith@getpawprint.com",
  "phone": "555-555-5555",
  "pet_id": 3,
  "pet_name": "Pumpkin",
  "pet_species": "Cat",
  "pet_breed": "Domestic Shorthair",
  "place_id": 4,
  "status": "new",
  "created_at": "2019-01-15T09:15:00Z",
  "updated_at": "2019-01-16T08:30:00Z",
  "description": "Wellness exam",
  "time": [
    "2018-04-14T11:00:00-08:00",
    "2018-04-14T11:15:00-08:00",
  ],
  "notes": "Note to the vet",
  "accepted_time": "12:15pm",
  "admin_id": 15,
  "admin_name": "Cassie"
}
```

Gets a single appointment request's details.

### HTTP Request
`GET /admin/v2/appointment_requests/:appointment_request_id`

## Get insurance claims

```json
[
  {
    "id": 549,
    "user_id": 948,
    "user_full_name": "John Smith",
    "pet_id": 21,
    "pet_name": "Zorro",
    "place_id": 1,
    "place_name": "Jellystone Animal Hospital",
    "insurance_id": 5,
    "insurance_name": "Pawprint Farm",
    "admin_id": 15,
    "admin_name": "Cassie",
    "status": "new",
    "created_at": "2019-01-15T09:15:00Z",
    "updated_at": "2019-01-16T08:30:00Z"
  }
]
```

Gets a list of insurance claims.

### HTTP Request
`GET /admin/v2/claims`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | Sort fields separated by commas, e.g. `sort_by=updated_at:desc,user_name:asc`
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er","place_id":27015}`

## Get an insurance claim

```json
{
  "id": 326,
  "user_id": 37683,
  "pet_id": 103843,
  "place_id": 27015,
  "pet_insurance_id": 2834,
  "visit_type": "wellness",
  "symptoms_start_date": "2018-11-01",
  "diagnosis": "Gastroenteritis",
  "is_new_condition": true,
  "was_claimed_before": false,
  "invoice_date": "2018-11-10",
  "invoice_total": "253.87",
  "treatment_start_date": "2018-11-01",
  "treatment_end_date": "2018-11-08",
  "signature": "https://pawprint-request-signature.s3.amazonaws.com/37683-1551947544647.png",
  "should_create_request": false,
  "status": "pending",
  "link": "https://pawprint-insurance-claim.s3.amazonaws.com/326_b6WRCGEevJuCqDtEsm55-8MW6DwDOZdF.pdf",
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
      "id": 43919,
      "status": "pending vet",
      "place_name": "Peanut Butter Veterinary Clinic"
    }
  ]
}
```

Gets a single insurance claim's details.

### HTTP Request
`GET /admin/v2/claims/:claim_id`

## Get intake forms

```json
[
  {
    "id": 549,
    "user_id": 948,
    "user_full_name": "John Smith",
    "pet_id": 21,
    "pet_name": "Zorro",
    "partner_id": 3,
    "partner_name": "Megavet Co.",
    "status": "pending",
    "created_at": "2019-01-15T09:15:00Z",
    "updated_at": "2019-01-16T08:30:00Z",
    "appointment": {
      "date": "2019-04-22",
      "time": "16:45",
      "timezone": "America/Los Angeles",
      "place_id": 41310,
      "place_name": "Megavet of Lakeshire"
    }
  }
]
```

Gets a list of intake forms.

### HTTP Request
`GET /admin/v2/intake`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | Sort fields separated by commas, e.g. `sort_by=updated_at:desc,user_name:asc`
filter | string? | Filter fields separated by commas, e.g. `filter={"place_name":"banf","pet_name":"er","place_id":27015}`

## Get an intake form

```json
{
  "id": 549,
  "user_id": 948,
  "user_full_name": "John Smith",
  "pet_id": 21,
  "pet_name": "Zorro",
  "partner_id": 3,
  "partner_name": "Megavet Co.",
  "status": "pending",
  "created_at": "2019-01-15T09:15:00Z",
  "updated_at": "2019-01-16T08:30:00Z",
  "appointment": {
    "date": "2019-04-22",
    "time": "16:45",
    "timezone": "America/Los Angeles",
    "place_id": 41310,
    "place_name": "Megavet of Lakeshire"
  },
  "requests": [
    {
      "id": 43919,
      "status": "pending vet",
      "place_name": "Peanut Butter Veterinary Clinic"
    }
  ]
}
```

Gets an individual intake form.

### HTTP Request
`GET /admin/v2/intake/:intake_id`
