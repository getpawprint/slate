# Vet intake portal
This API is for the vet side of intakes - account management, intake summaries and intake detail.

## Login
> Request example

```json
{
  "email": "demo2@getpawprint.com",
  "password": "p4ssw0rd"
}
```

> Response example

```json
{
  "token": "eYJazo.sometoken.oawf8aliwf8aZ_-w3BmAS"
}
```

Login for partners for the records portal. Each place has its own separate login.
Vet groups will have a single entry in the `partner` table but multiple `partner_place` entries (which store login info), while
single vets will have a single entry in both the `partner` and `partner_place` tables.
Returns a JWT to be used for authenticated partner calls.

### HTTP Request
`POST /partners/login`

## Change password
> Request example

```json
{
  "old_password": "passw3rd",
  "password": "p4ssw0rd"
}
```

> Response example

```json
(none)
```

Partner self-service password change.

### HTTP Request
`POST /partners/password`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
old_password | string | Partner place's old password
password | string | Partner place's new password.

## Send password reset token

> Request example

```json
{
  "email": "demo@getpawprint.com"
}
```

> Response example

```json
(none)
```

Emails a password reset token to the partner place's email address.
Always returns HTTP 200.

### HTTP Request
`POST /partners/reset`

## Validate password reset token

> Request example

```json
(none)
```

> Response example

```json
{
  "name": "Test Vet",
  "email": "demo@getpawprint.com"
}
```

Shows information about the vet associated with a password reset token. If the reset token is not found or it is expired, HTTP 404 is returned.

### HTTP Request
`GET /partners/reset/:token`

## Reset forgotten password from token
> Request example

```json
{
  "password": "new_p4ssw0rd"
}
```

> Response example

```json
(none)
```

Resets the password on the account that matches the given token. Returns HTTP 404 if the token was not found; HTTP 200 otherwise.

### HTTP Request
`POST /partners/reset/:token`

## Reset forgotten password (admin)
> Request example

```json
{
  "password": "p4ssw0rd"
}
```

> Response example

```json
(none)
```

For admins to manually set a new password for a partner place.

### HTTP Request
`POST /admin/partners/:place_id/reset`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string | Partner place's email address.
password | string | Partner place's password.


## Get partner place info
> Response example

```json
{
  "name": "Test Animal Clinic",
  "place_id": "27015",
  "partner_id": 5,
  "partner_name": "Pawprint Farm",
  "banner_image": "https://www.getpawprint.com/images/banner.jpg",
  "email": "demo@getpawprint.com",
  "appointment_types": [
    {
      "id": 1",
      "description": "Wellness exam"
    },
    {
      "id": 2",
      "description": "Illness/injury"
    },
  ]
}
```

Gets partner place info for the caller identifed in the auth header, like place name, partner ID, partner name and banner image.

### HTTP Request
`GET /partners/info`

## Get list of intakes
> Response example

```json
{
  "data": [
    {
      "id": 93,
      "user_full_name": "John Smith",
      "pet_name": "Hiro",
      "pet_species": "dog",
      "pet_breed": "Shiba Inu",
      "status": "in progress",
      "created_at": "2019-04-17T23:58:12.932Z",
      "updated_at": "2019-04-17T23:58:19.764Z",
      "appointment": {
        "date": "2019-04-20",
        "time": "10:00 AM"
      }
    }
  ],
  "count": 1
}
```

Gets information about intakes for a partner place. Paginated.

*Filters:*
Multiple filters are joined by AND.

- `status` - One of `missing info`, `pending`, `in progress`, `complete` and `cancelled`
- `appointment_date` - Restricts results down to a specific day; format is `YYYY-MM-DD`. *Sort field is locked to `appointment_date` and direction defaults to `desc` when this filter is selected, but the direction remains changeable (`asc`/`desc`).*
- `pet_name` - Peforms partial name on pets. (e.g. `chi` will match both `Mochi` and `Chip`)
- `user_name` - Performs partial name match on user first name OR last name (e.g. 'smi' will match both "Jasmine Wong" and "Jane Smith", but not "Jess Milan")


*Sorting:*
Direction is specified by `asc` or `desc`.

- `appointment_date` - Sorts by appointment date. Intakes with no appointment date are automatically excluded.
- `created_at` - Sorts by `created_at` date, which is when the form was created by the vet, or generated based on synced PMS data.
- `updated_at` - Sorts by `updated_at` date, which is the timestamp of the last admin action (or `created_at` at the earliest).

### HTTP Request
`GET /partners/intake`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | int? | Limits number of results.
$skip | int? | Offsets number of results.
sort_by | string? | Default is `{"updated_at":"desc"}`. This parameter is ignored if the `date` filter is specified unless it is `appointment_date`, which allows the sort direction to be changed.
filter | string? | JSON object, where the key is the filter name and the value is the filter value, e.g. `&filter={"status":"missing info"}`.

## Get intake details
> Response example

```json
{
  "appointment": {
    "date": "2019-04-20",
    "time": "10:00 AM",
    "type": "Wellness exam",
    "reason": "Puppy shots"
  },
  "summary": {
    "id": 31,
    "external_id": "-HiX2CpDBA_tukyYkldcN5gGwqwPyAsh",
    "admin_name": "Eric Choi",
    "date": "05-08-2019",
    "pet": {
      "name": "Rocky",
      "image": "https://pawprint-user-upload.s3-us-west-2.amazonaws.com/65389-39561-1496970785752.jpg",
      "birthdate": "11-01-2005",
      "age": "13 yo",
      "adoption_date": null,
      "gender": "Male",
      "neuterString": ", neutered",
      "species": "Dog",
      "breed": "American Staffordshire Terrier",
      "color": "Tri",
      "weight": null,
      "microchip": "1337"
    },
    "user": {
      "first_name": "Momo",
      "last_name": "Nguyen",
      "phone": "(555) 555-5504",
      "email": "momonguyen@getpawprint.com"
    },
    "places": [
      {
        "name": "Aborn Pet Clinic",
        "phone": "(408) 270-4600"
      },
      {
        "name": "Antioch animal hospital",
        "phone": "(630) 307-9115"
      },
      {
        "name": "VIP Pet Care",
        "phone": "(800) 427-7973"
      }
    ],
    "medications": "Enacard, Vasotec",
    "user_medications": "Enacard, Vasotec",
    "labs": [
      {
        "name": "SNAP 4Dx Test",
        "date": "12-27-2014",
        "expiry": "12-27-2015",
        "expiryStatus": "expired",
        "primary_condition": "Heartworm"
      },
      {
        "name": "Fecal Exam",
        "date": "10-29-2017",
        "expiry": "10-29-2018",
        "expiryStatus": "expired",
        "primary_condition": null
      },
      {
        "name": "Urine",
        "date": "-",
        "expiry": "-",
        "expiryStatus": "missing"
      },
      {
        "name": "CBC",
        "date": "-",
        "expiry": "-",
        "expiryStatus": "missing"
      },
      {
        "name": "Chemistry",
        "date": "-",
        "expiry": "-",
        "expiryStatus": "missing"
      },
      {
        "name": "Thyroid Level",
        "date": "-",
        "expiry": "-",
        "expiryStatus": "missing"
      },
      {
        "name": "Heartworm Test",
        "date": "-",
        "expiry": "-",
        "expiryStatus": "missing"
      }
    ],
    "vaccinations": [
      {
        "name": "Rabies",
        "date": "04-19-2019",
        "expiry": "04-19-2020",
        "expiryStatus": "active",
        "primary_condition": "Rabies"
      },
      {
        "name": "Distemper",
        "date": "-",
        "expiry": "-",
        "expiryStatus": "missing",
        "primary_condition": "Distemper-parvo"
      },
      {
        "name": "Bordetella",
        "date": "-",
        "expiry": "-",
        "expiryStatus": "missing",
        "primary_condition": "Bordetella"
      }
    ],
    "review_notes": "Rocky is 80% blind in left eye. No hearing",
    "pdfs": [
      "https://pawprint-request-pdf.s3.amazonaws.com/123.pdf",
      "https://pawprint-request-pdf.s3.amazonaws.com/456.pdf",
      "https://pawprint-request-pdf.s3.amazonaws.com/789.pdf",
      "https://pawprint-request-pdf.s3.amazonaws.com/0ab.pdf",
      "https://pawprint-request-pdf.s3.amazonaws.com/cde.pdf"
    ]
  }
}
```
Get detailed information about a specific intake for a partner place. There are two main fields - `appointment` and `summary`; `summary` is the same Pawprint Summary object that gets passed to the PDF generator in the admin portal. HTTP 403 is returned if the `intake_id` does not belong to the partner place.

### HTTP Request
`GET /partners/intake/:intake_id`


## Update intake (vet)
> Request example

```json
{
  "status": "cancelled",
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "206-555-5555",
  },
  "pet": {
    "name": "Casper"
  },
	"appointment": {
    "date": "2019-07-20",
    "time": "15:30",
    "timezone": "America/Los_Angeles",
    "type": "Injury/Illness",
    "reason": "Mass on right hind leg"
  }
}
```

> Response example

```json
(none)
```

Allows vets to update an intake, but only when `status` is `missing info` or `pending user`.
`user.email` and `user.phone` can only be updated when `status` is `missing info`, and either of those fields is entered,
the intake will automatically enter `pending user` status and the `intake-form` email/SMS will be sent to the user.

### HTTP Request
`PUT /partners/intake/:intake_id`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
status | string? | Only accepted value is `cancelled`.
user | object? | User details
user.first_name | string? | User first name
user.last_name | string? | User last name
user.email | string? | User's email address
user.phone | string? | User's phone number
pet | object? | Pet details
pet.name | string? | Pet name
appointment | object? | Appointment details
appointment.date | string? | Date portion of the appointment, e.g. "2019-07-20".
appointment.time | string? | Time portion of the appointment in 24 hour time, e.g. "3:30 pm" or "9:45 am".
appointment.timezone | string? | https://en.wikipedia.org/wiki/List_of_tz_database_time_zones.
appointment.type | string? | Type of appointment.
appointment.reason | string? | Reason for the appointment.