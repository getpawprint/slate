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
      "confirmation_code": "NCCL7A",
      "user_full_name": "John Smith",
      "user_email": "johnsmith@snoutid.com",
      "user_phone": "(425) 753-6174",
      "user_car_make_model": "Grey Subaru Outback",
      "normalized_phone": "+14257536174",
      "vet_user_id": 84159,
      "unread_sms": true,
      "pet_name": "Hiro",
      "pet_species": "dog",
      "pet_breed": "Shiba Inu",
      "status": "in progress",
      "questionnaire_status": "pending user",
      "link": null,
      "created_at": "2019-04-17T23:58:12.932Z",
      "updated_at": "2019-04-17T23:58:19.764Z",
      "appointment": {
        "date": "2019-04-20",
        "time": "10:00 AM"
      },
      "has_new_files": true,
      "show_dot": true,
      "file_count": 5,
      "empty_reason": "New puppy",
      "completed_requests": 1,
      "total_requests": 2,
      "last_email_at": "2020-01-20T10:01:12Z",
      "last_sms_at": "2020-01-20T10:02:00Z",
      "completed_forms": 1,
      "total_forms": 2,
      "checkin": {
        "checked_in_at": "2020-01-20T16:30:12Z",
        "location": "Next to the handicap spot"
      }
    }
  ],
  "count": 1
}
```

Gets information about intakes for a partner place. Paginated. If `file_count` is 0, then `empty_reason` will be populated; otherwise it will be `null`.

*Filters:*
Multiple filters are joined by AND.

- `status` - `missing info`, `pending`, `in progress`, `complete` or `cancelled`; can be combined with a comma for OR, e.g. `filter={"status":"pending,in progress"}`
- `appointment_date` - Restricts results down to a specific day; format is `YYYY-MM-DD`. *Sort field is locked to `appointment_date` and direction defaults to `desc` when this filter is selected, but the direction remains changeable (`asc`/`desc`).*
- `pet_name` - Peforms partial name on pets. (e.g. `chi` will match both `Mochi` and `Chip`)
- `user_name` - Performs partial name match on user first name OR last name (e.g. 'smi' will match both "Jasmine Wong" and "Jane Smith", but not "Jess Milan")
- `name` - Performs partial name match on user first name OR last name OR pet name
- `has_new_files` - boolean; returns only the intakes that should be highlighted with a blue dot indicator. The definition of the indicator is subject to change but is currently a combination of unopened files and changed user contact info.
- `show_dot` - the new name for the `has_new_files` filter


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

## Get intake counts
> Response example

```json
{
  "new_files": 6,
  "staff_attn": 12,
  "pending": 89
}
```

Returns the number of intakes with certain statuses, regardless of date. An intake may be counted for multiple statuses.

### HTTP Request
`GET /partners/intake/count`


## Get list of intakes V2
> Response example

```json
[
  {
    "id": 93,
    "status": {
      "archived": false,
      "pending": true,
      "staff_attn": true,
      "new_files": true
    },
    "user": {
      "id": 37683,
      "first_name": "John",
      "last_name": "Smith",
      "email": "johnsmith@snoutid.com",
      "phone": "(555) 550-1234",
      "normalized_phone": "+15555501234"
    },
    "pet": {
      "name": "Hachi",
      "species": "dog",
      "breed": "Akita",
      "image_url": "https://s3.aws.amazon.com/snoutid-profile-pic/AbXjoie.jpg"
    },
    "appointment": {
      "datetime": "2020-01-20T15:45:00-04:00",
      "type": "Wellness Exam",
      "last_email_at": "2020-01-20T10:01:12-04:00",
      "last_sms_at": "2020-01-20T10:01:28-04:00",
      "confirmation_code": "NCC1701D"
    },
    "files": [
      {
        "name": "New Client Form",
        "status": "complete",   // "pending", "complete" or "staff_attn"
        "url": "https://s3.aws.amazon.com/snoutid-new-client/AbXjoie.pdf",
        "opened": true
      },
      {
        "name": "Snapshot",
        "status": "complete",
        "url": "https://s3.aws.amazon.com/snoutid-new-client/AbXjoie.pdf",
        "opened": false
      },
      {
        "name": "Questionnaire",
        "status": "pending",
        "url": null,
        "opened": null
      },
      {
        "name": "Surgery Estimate",
        "status": "staff_attn",
        "url": null,
        "opened": null
      },
      {
        "name": "Anesthesia Consent",
        "staus": "pending",
        "url": null,
        "opened": null
      },
      {
        "name": "Records from ABC Animal Hospital",
        "status": "complete",
        "url": "https://s3.aws.amazon.com/snoutid-snapshot/AbXjoie.pdf",
        "opened": true
      }
    ]
  }
]
```

Gets list of intakes for the given day for the vet. The `:date` parameter is an ISO-8601 date (without time), e.g. '2020-11-15'; if not specified, it defaults to the current day in the vet's time zone.

**`status` filter**

Value | Description
----- | -----------
default | Returns everything except archived intakes.
all | Returns everything.
pending | Returns intakes that are still in progress, but don't require the practice's attention. Specifying this causes the date parameter to be ignored.
staff_attn | Returns intakes that require the practice's attention (i.e. form needs to be filled out). Specifying this causes the date parameter to be ignored.
new_files | Returns unarchived intakes that have undownloaded files. If an archived intake has new files attached to it (e.g. client filled out a form late), the intake will automatically be unarchived. Specifying this causes the date parameter to be ignored.

### HTTP Request
`GET /partners/v2/intake/:date`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
filter | string? | JSON object, where the key is the filter name and the value is the filter value, e.g. `&filter={"status":"staff_attn"}`.

## Archive an intake
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Archives an intake. All files will be marked as downloaded. If a new file is attached to the intake after it is archived
(e.g. record request was fulfilled or client filled out a form), it will be unarchived on the backend.

### HTTP Request
`POST /partners/intake/:intake_id/archive`


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
  "checkin": {
    "checked_in_at": "2020-01-20T16:30:12Z",
    "location": "Next to the handicap spot"
  },
  "form_instances": [{
    "id": 12,
    "name": "Surgery estimate",
    "status": "cancelled",
    "completed_at": null
  },
  {
    "id": 13,
    "name": "Anesthesia consent",
    "status": "pending vet",
    "completed_at": null
  },
  {
    "id": 14,
    "name": "Surgery estimate",
    "status": "completed",
    "completed_at": "2020-08-22T10:34:45Z"
  }],
  "summary": {
    "id": 31,
    "external_id": "-HiX2CpDBA_tukyYkldcN5gGwqwPyAsh",
    "admin_name": "Eric Choi",
    "date": "05-08-2019",
    "status": "complete",
    "questionnaire_status": "complete",
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
      "microchip": "1337",
      "type": "Indoor"
    },
    "user": {
      "id": 37683,
      "first_name": "Momo",
      "last_name": "Nguyen",
      "phone": "(555) 555-5504",
      "email": "momonguyen@getpawprint.com",
      "birthdate": "1990-01-01",
      "name_changed": false,
      "email_changed": true,
      "phone_changed": false,
      "address_changed": false,
      "car_make_model": "Ford Taurus, grey"
    },
    "secondary": {
      "first_name": "Popo",
      "last_name": "Nguyen",
      "phone": "(555) 555-6616",
    },
    "marketing": {
      "media_opt_in": false,
      "sms_opt_in": true,
      "referral": "Word of mouth"
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
      {
        "id": 41256,
        "link": "https://pawprint-request-pdf.s3.amazonaws.com/48905-1565808703446.pdf",
        "title": "Cat Care Professionals: 08/14/2019",
        "description": "Medical history",
        "created_at": "2019-08-14T18:51:43.895Z"
      }
    ],
    "user_files": [
      {
        "name": "Rabies records",
        "link": "https://pawprint-user-upload.s3.amazonaws.com/495-ABC-vaccines.pdf"
      }
    ],
    "new_client_link": "https://pawprint-intake-new-client.s3.amazonaws.com/New_Client_Form_-_Milo_Choi_NeIyuw.pdf",
    "signed_forms": [
      {
        "name": "Surgery Risk Acknowledgement",
        "link": "https://pawprint-user-upload.s3.amazonaws.com/495-ABC-vaccines.pdf",
        "created_at": "2020-04-19T12:59:323Z"
      }
    ],
    "opened_files": [
      "https://pawprint-request-pdf.s3.amazonaws.com/48905-1565808703446.pdf",
      "https://pawprint-user-upload.s3.amazonaws.com/495-ABC-vaccines.pdf"
    ],
    "questionnaire": {
      "name": "Pet questionnaire",
      "responses": [{
        "question": {
        "name": "thirst",
        "label": "Changes in thirst",
        "input_type": "select",
        "default_answer": "Same",
        "options": ["Less", "Same", "More"]
        },
        "response": "More"
      },
      {
        "question": {
        "name": "medication",
        "input_type": "medications"
        },
        "response": [{"id": 42, "refill": true}, {"id": 37, "refill": false}]
      },
      {
        "question": {
        "name": "activity_level",
        "label": "Activity level",
        "input_type": "checkboxes",
        "options": ["Park","Leash Walks","Daycare"]
        },
        "response": ["Park","Leash Walks"]
      }]
    }
  }
}
```
Get detailed information about a specific intake for a partner place. There are three main fields - `appointment` and `summary`; `summary` is the same Pawprint Summary object that gets passed to the PDF generator in the admin portal.
`questionnaire` will be null if the client did not fill out an intake questionnaire.
HTTP 403 is returned if the `intake_id` does not belong to the partner place.

### HTTP Request
`GET /partners/intake/:intake_id`

## Log file open event
> Request example

```json
{
  "url": "https://pawprint-user-upload.s3.amazonaws.com/495-ABC-vaccines.pdf",
  "opened": true
}
```

> Response example

```json
(none)
```

Sets opened/not opened status on a file within an intake. The file can be any URL - the profile picture, the snapshot PDF link,
a user-uploaded file, or a file from a record request.

### HTTP Request
`POST /partners/intake/:intake_id/file_open`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
url | string | URL of the file from a request 
opened | bool? | Defaults to `true` if not specified; `false` will set it back to new.

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

## Re-send intake reminder
> Request example

```json
{
  "phone": true,
  "email": "override_email@getpawprint.com"
}
```

> Response example

```json
(none)
```

Resends a reminder email or text to the client in the intake. If the `email` or `phone` parameters are strings, the email address or phone number will be overridden; otherwise, an email or SMS is sent to the contact info on file.
The intake's email and/or phone will be updated in the database if overrides were provided.

### HTTP Request
`POST /partners/intake/:intake_id/reminder`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | boolean or string | Send by email; if string, overrides email address
phone | boolean or string | Send by SMS; if string, overrides phone number

## Download all files in a .zip archive
> Response example

```json
(binary data, Content-Type: application/zip)
```

Generates a .zip archive on demand for all files in an intake. Files include (if available):
- Intake form
- New client form
- Pet photo
- Record request PDFs
- User uploaded files

### HTTP Request
`GET /partners/intake/:intake_id/zip`

## Check in/un-check in a client manually
> Request example

```json
{
  "status": true
}
```

> Response example

```json
(none)
```

Sets checked-in or not-checked-in status of all the intakes in a given bundle.

### HTTP Request
`POST /partners/bundle/:bundle_id/checkin`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
status | bool | New checkin status for the bundle

## Websocket updates
> Notification example

```json
{
  "data": {
    "id": 93,
    "confirmation_code": "NCCL7A",
    "user_full_name": "John Smith",
    "pet_name": "Hiro",
    "pet_species": "dog",
    "pet_breed": "Shiba Inu",
    "status": "in progress",
    "link": null,
    "created_at": "2019-04-17T23:58:12.932Z",
    "updated_at": "2019-04-17T23:58:19.764Z",
    "appointment": {
      "date": "2019-04-20",
      "time": "10:00 AM"
    },
    "has_new_files": true,
    "show_dot": true,
    "file_count": 5,
    "empty_reason": "New puppy",
    "completed_requests": 1,
    "total_requests": 2,
    "last_email_at": "2020-01-20T10:01:12Z",
    "last_sms_at": "2020-01-20T10:02:00Z"
  },
  "event": "updated"
}
```

Connect to a websocket to receive creation and update notifications. The response schema for the `data` field is the same
as the intake summary object in `GET /partners/intake`.

`event` is either `created` or `updated`. In most cases, it will be `updated`; the `created` notification only fires when an embedded/standalone intake is created (followed immediately by an `updated` notification).

### Connection Request
`ws://www.getpawprint.com/ws/partners/intake?access_token=${authToken}`
