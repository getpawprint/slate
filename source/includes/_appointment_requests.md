# Appointments
Appointment booking, cancellations and status.

## Get appointment types

> Response example

```json
[
    {
        "id": 1,
        "place_id": null,
        "description": "Wellness exam",
        "created_at": "2017-10-11T00:53:50.425Z",
        "updated_at": "2017-10-11T00:53:50.425Z"
    },
    {
        "id": 2,
        "place_id": null,
        "description": "Illness/injury",
        "created_at": "2017-10-11T00:53:50.425Z",
        "updated_at": "2017-10-11T00:53:50.425Z"
    },
    {
        "id": 3,
        "place_id": null,
        "description": "Vaccines",
        "created_at": "2017-10-11T00:53:50.425Z",
        "updated_at": "2017-10-11T00:53:50.425Z"
    },
    {
        "id": 4,
        "place_id": null,
        "description": "New client",
        "created_at": "2017-10-11T00:53:50.425Z",
        "updated_at": "2017-10-11T00:53:50.425Z"
    },
    {
        "id": 5,
        "place_id": null,
        "description": "Tech visit",
        "created_at": "2017-10-11T00:53:50.425Z",
        "updated_at": "2017-10-11T00:53:50.425Z"
    },
    {
        "id": 6,
        "place_id": null,
        "description": "Dental",
        "created_at": "2017-10-11T00:53:50.425Z",
        "updated_at": "2017-10-11T00:53:50.425Z"
    },
    {
        "id": 7,
        "place_id": null,
        "description": "Emergency",
        "created_at": "2017-10-11T00:53:50.425Z",
        "updated_at": "2017-10-11T00:53:50.425Z"
    }
]
```

Gets appointment types from the `appointment_type` table. `place_id` null is the default set of appointment types.
There used to be plans to support Pawprint vets customizing their own appointment types, but that didn't happen.

### HTTP Request
`GET /place/appointment_type_options`


## Book an appointment
> Request example

```json
{
  email: 'john@getpawprint.com',
  phone: '425-555-1234',
  pet_name: 'Pumpkin',
  pet_species: 'Cat',
  pet_breed: 'Domestic shorthair',
  appointment_type_id: 2,
  time: ['2017-12-15T08:00:00-06:00'],
  notes: 'Testing',
  user_name: 'John Smith'
}
```

> Response example

```json
(none)
```

Creates an appointment request in the `appointment_request` table. If the vet is a Pawprint vet, an email is automatically sent to the vet.
Otherwise, the appointment request will appear on the admin dashboard for someone to handle. Some Pawprint
vets are walk-in only, so the app should block appointment requests for these places and display the
`place.custom_message` message for that vet.

<aside class="notice">
This is a public API and does not require authentication. If this is called on behalf of a logged in user,
the client is expected to populate fields based on the user and pet profile.
</aside>

### HTTP Request
`POST /user/appointment_requests`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string | Requester's email address
phone | string | Requester's phone number
pet_id | int? | ID from the `pet` table, if available.
pet_name | string | Pet's name
pet_species | string | Cat, dog, rabbit, etc.
pet_breed | string | Pet breed, e.g. Chihuahua, Persian, Angora
appointment_type_id | int | ID from the `appointment_type` table (see above).
time | array of datetimes | Requester's desired times (vet/admin should pick one)
notes | string? | Notes to the vet
user_name | string | Requester's name


## Get appointment requests

> Response example

```json
[
    {
        "id": 700,
        "name": "Super Test Vet",
        "address": "123 Main Street, Ligonier, IN 46767",
        "notes": null,
        "time": [
            "2018-12-07T10:00:00-05:00",
            "2018-12-07T11:00:00-05:00",
            "2018-12-07T12:00:00-05:00",
            "2018-12-07T13:00:00-05:00"
        ],
        "status": "new",
        "pet_name": "Mr. Peabody",
        "pet_id": 12,
        "accepted_time": null,
        "description": "Vaccines"
    }
]
```

Gets appointment requests for the "Book an appointment" feature,
regardless of new/pending/complete/cancelled status.

### HTTP Request
`GET /user/appointment_requests`

## Get upcoming appointments

> Response example

```json
[
    {
        "id": 309185,
        "pet_id": null,
        "pet_name": "Cuddles",
        "place_name": "Pawprint Demo Vet",
        "address": "1020 NW 192nd St Suite L, Edmond, OK 73012",
        "date": "2018-12-20T16:00:00"
    },
    {
        "id": 309642,
        "pet_id": 612,
        "pet_name": "Max",
        "place_name": "Pawprint Demo Vet",
        "address": "1020 NW 192nd St Suite L, Edmond, OK 73012",
        "date": "2018-12-20T16:00:00"
    }
]
```

Gets all upcoming appointments, including from the `vet_schedule` table. Excludes cancelled or rescheduled appointments
and excludes appointment requests that haven't been completed by an admin yet.

`date` values may or may not have a timezone. This is because appointments in `appointment_request` are timezone-aware (a fixed point in time),
while appointments in the `vet_schedule` table are naive (not timezone-aware). If `date` has no time zone, it should be assumed to be
in the client's local time zone. If `date` does have a time zone, it should be converted to the client's local time zone before rendering.

### HTTP Request
`GET /user/upcoming_appointments`

## Cancel an appointment request
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Cancels an appointment request.

### HTTP Request
`POST /user/appointment_requests/:id`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
id | int | ID from the `appointment_request` table.

## Confirm a Pawprint vet appointment

> Response example

```json
(none)
```

Confirms a pending Pawprint vet appointment via confirmation code. Appointment confirmations are emailed or SMSed to clients
up to 48 hours in advance. SMS clients can either respond '1' to confirm an appointment, or can cancel with '2'.
Email clients can click the confirmation link that goes to https://www.getpawprint.com/appointment_confirmation/:code.
Confirmation codes are randomly generated and stored in the `vet_schedule.confirmation_code` field. One confirmation code
can cover multiple appointments if a client has multiple appointments with the same vet on the same day.

<aside class="notice">
This is a public API and does not require authentication.
</aside>

### HTTP Request
`GET /appointment_confirmation/:code`
