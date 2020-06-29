# Legacy portal
Redistribute/reorganize these as they are integrated into the vet portal

## Get appointments

> Response example

```json
[
    {
        "id": 223957,
        "date": "2017-12-03T00:00:00.000Z",
        "duration": null,
        "status": "confirmed",
        "props": null,
        "pet_name": "Ein DataDog",
        "first_name": "Eric",
        "last_name": "Choi",
        "email": "eli@scoutvet.com",
        "phone": "604-364-8024"
    }
]
```

Returns scheduled appointments for a vet, over a time range.

### HTTP Request
`GET /partners/schedule`

## Get reminders for approval

> Response example

```json
[
    {
        "id": 2,
        "firstName": "Jared",
        "lastName": "Luxenberg",
        "phone": "+17817184182",
        "platform": null,
        "push_token": null,
        "email": "jared@jaredlux.com",
        "user_id": null,
        "contact_token": "xxx",
        "activation_code": "872070",
        "email_permission": true,
        "push_permission": false,
        "sms_permission": true,
        "vetId": 48621,
        "vetBanner": "https://s3-us-west-1.amazonaws.com/pawprint-place-img/AnimalEmpireLogo.png",
        "vetEmail": "animalempire@getpawprint.com",
        "vetName": "Animal Empire Veterinary Clinic",
        "vetPhone": "7188999200",
        "vetPlaceId": 4665,
        "vetShortName": "Animal Empire",
        "appointmentCTA": "Please call (718) 899-9200",
        "appointmentWebsite": null,
        "reminders": [
            {
                "expiry": "2017-10-18",
                "expiryDescriptionHtml": "<b>is due in 1 month</b>",
                "petName": "Leki",
                "pet_id": null,
                "status": "Canine Rabies Vaccination"
            }
        ],
        "approved": null,
        "created_at": "2017-09-18T23:17:27.256Z",
        "updated_at": "2017-09-18T23:17:27.256Z",
        "vet_user_id": null
    }
]
```

Returns list of reminders for a vet to approve.

### HTTP Request
`GET /partners/reminders`

## Client statistics

> Response example

```json
{
    "totalUsers": "5",
    "totalActiveUsers": "2",
    "vetName": {
        "shortname": "Test Vet!"
    }
}
```

Lists client statistics. "Active" users are simply users that have linked a Pawprint account to their `vet_user` account.

### HTTP Request
`GET /partners/user_stats`

## List clients

> Response example

```json
{
    "total": "1",
    "clients": [
        {
            "id": 84159,
            "email": "echoi@scoutvet.com",
            "user_id": null,
            "first_name": "Eric",
            "last_name": "Choi",
            "street_address": "1100 106th Ave NE",
            "street_address2": null,
            "country": null,
            "city": "Bellevue",
            "state": "WA",
            "zip": "98004",
            "phone": "425-746-6958",
            "do_not_contact": false,
            "code": null,
            "last_notified": null
        }
    ]
}
```

Returns paginated list of clients from the `vet_user` table.
Currently does not include clients that were created in the ScoutVet database via standalone, Vetstoria or vet portal intakes.

### HTTP Request
`GET /partners/clients`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
order_by | string? | Corresponds to a database column; defaults to `last_name`
dir | string? | `asc` or `desc`; defaults to `asc`
limit | int? | Number of results to return
offset | int? | Start index
filter | string? | Filter by last name (case insensitive)

## List clients by last contacted date

> Response example

```json
{
    "clients": [
        {
            "id": 84159,
            "user_id": null,
            "first_name": "Eric",
            "last_name": "Choi",
            "email": "echoi@getpawprint.com",
            "phone": "425-746-6958",
            "street_address": "1100 106th Ave NE",
            "street_address2": null,
            "city": "Bellevue",
            "state": "WA",
            "zip": "98004",
            "last_contacted": "2018-08-28T00:39:53.305Z",
            "next_visit": null,
            "primary_phone": null
        },
        {
            "id": 10369,
            "user_id": null,
            "first_name": "Demo",
            "last_name": "Dee",
            "email": "demo2@getpawprint.com",
            "phone": null,
            "street_address": "24 Beast Mode Way",
            "street_address2": null,
            "city": "Oakland",
            "state": "CA",
            "zip": "94666",
            "last_contacted": "2017-11-01T00:00:00.000Z",
            "next_visit": null,
            "primary_phone": null
        }
    ]
}
```

Returns list of last contacted clients from the vet_user table.
Currently does not include clients that were created in the ScoutVet database via standalone, Vetstoria or vet portal intakes.
Users with unread messages are at the top of the order, followed by users ordered by last contacted date
If `filter` query parameter is specified: Number of unread messages becomes irrelevant and filter is applied

### HTTP Request
`GET /partners/clients/last_contact`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
limit | int? | Number of results to return
offset | int? | Start index
filter | string? | Filter by first name concatenated with last name (case insensitive)

## List clients by last contacted date V2

> Response example

```json
[
    {
        "id": 84159,
        "email": "echoi@scoutvet.com",
        "phone": "(555) 555-1234",
        "first_name": "Eric",
        "last_name": "Choi",
        "message_body": "Are you here yet?",
        "read": false,
        "last_contact_at": "2020-06-28T05:23:32Z"
    },
    {
        "id": null,
        "email": null,
        "phone": "(555) 555-3456",
        "first_name": null,
        "last_name": null,
        "message_body": "I'm bringing in a kitten today",
        "read": true,
        "last_contact_at": "2020-06-28T05:23:32Z"
    }
]
```

Returns paginated list of clients from the `vet_user` table, combined with unknown phone numbers, sorted by
last contact date (most recent first), and with the latest message to or from that client.
Phone numbers not associated with a `vet_user` will have a null `id` and only a phone number.
Currently does not include clients that were created in the ScoutVet database via standalone, Vetstoria or vet portal intakes.

### HTTP Request
`GET /partners/clients`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top| int? | Number of results to return; defaults to 10
filter | string? | Filter by first name or last name or phone number (case insensitive)

## Get client

> Response example

```json
{
    "id": 84159,
    "first_name": "Eric",
    "last_name": "Choi",
    "street_address": "1100 106th Ave NE",
    "street_address2": null,
    "country": null,
    "city": "Bellevue",
    "state": "WA",
    "zip": "98004",
    "email": "echoi@getpawprint.com",
    "phone": "425-746-6958",
    "code": null,
    "phone_numbers": []
}
```

Gets a client from the `vet_user` table.

### HTTP Request
`GET /partners/clients/:vet_user_id`


## Get client's pets

> Response example

```json
{
    "pets": [
        {
            "name": "Frankie ",
            "birthdate": "2008-06-08T07:00:00.000Z",
            "gender": "m",
            "species": "Feline",
            "breed": "Domestic Shorthair (DSH)",
            "color": "black and white ",
            "weight": null,
            "neuter": true
        },
        {
            "name": "Anna Bell",
            "birthdate": "2003-06-08T07:00:00.000Z",
            "gender": "f",
            "species": "Feline",
            "breed": "Domestic Shorthair (DSH)",
            "color": "calico",
            "weight": null,
            "neuter": true
        }
    ]
}
```

Gets a client's pets from the `vet_pet` table.

### HTTP Request
`GET /partners/clients/:vet_user_id/pets`


## Get emails sent to a client

> Response example

```json
[
    {
        "message_content": "[{\"name\":\"VERIFICATIONLINK\",\"content\":\"https://bnc.lt/judk/r7czkcgHJP\"},{\"name\":\"FIRSTNAME\",\"content\":\"Eric\"}]",
        "message_type": "verify-account-link",
        "created_at": "2018-08-28T00:13:18.905Z"
    },
    {
        "message_content": "[{\"name\":\"VERIFICATIONLINK\",\"content\":\"https://bnc.lt/judk/GlbbRBlIJP\"},{\"name\":\"FIRSTNAME\",\"content\":\"Eric\"}]",
        "message_type": "verify-account-link",
        "created_at": "2018-08-28T00:28:31.709Z"
    },
    {
        "message_content": "[{\"name\":\"VERIFICATIONLINK\",\"content\":\"https://bnc.lt/judk/H1cToVZIJP\"},{\"name\":\"FIRSTNAME\",\"content\":\"Eric\"}]",
        "message_type": "verify-account-link",
        "created_at": "2018-08-28T00:37:37.822Z"
    },
    {
        "message_content": "[{\"name\":\"VERIFICATIONLINK\",\"content\":\"https://bnc.lt/judk/gKJ5nW9IJP\"},{\"name\":\"FIRSTNAME\",\"content\":\"Eric\"}]",
        "message_type": "verify-account-link",
        "created_at": "2018-08-28T00:39:53.305Z"
    }
]
```

Gets emails sent to a `vet_user` from the `delivery` table.

### HTTP Request
`GET /partners/clients/:vet_user_id/emails`


## Get client's appointments with confirmation/cancellations

> Response example

```json
{
    "data": [
        {
            "confirmation_status": "confirmed",
            "confirmed_at": "2020-06-18T21:41:41.031Z",
            "confirmation_platform": "sms",
            "type": "Wellness Exam"
        }
    ]
}
```

Gets appointments from the `vet_schedule` table where the `vet_user` has responded with confirm/cancel.

### HTTP Request
`GET /partners/clients/:vet_user_id/schedule`

## Get sent emails and SMSes for reminders

> Response example

```json
{
    "data": [
        {
            "id": 55485,
            "type": "email",
            "created_at": "2017-11-01T00:00:00.000Z",
            "message_content": "{\"recipient\":[{\"email\":\"demo2@getpawprint.com\"}],\"from\":{\"name\":\"Test Vet via Pawprint\",\"email\":\"test-vet@getpawprint.com\"},\"variables\":[{\"name\":\"OWNER\",\"content\":\"Demo Dee\"},{\"name\":\"VACCINES\",\"content\":\"<li>Chip's Heartworm Test <b>is due in 1 month</b> (11/30/2017)<li>Chip's K9 Distemper Parvo Vn Titer <b>is due in 1 month</b> (11/30/2017)\"},{\"name\":\"VETBANNER\",\"content\":\"http://www.getpawprint.com/images2/logo@2x.png\"},{\"name\":\"VETNAME\",\"content\":\"Test Vet\"},{\"name\":\"VETAPPOINTMENTWEBSITE\",\"content\":\"http://getpawprint.com\"},{\"name\":\"VETCONTACTCTA\",\"content\":\"Please call 555-555-1234 or email support@getpawprint.com\"},{\"name\":\"VETSIGNATURE\",\"content\":\"Test Vet\"},{\"name\":\"VETPHONE\",\"content\":null},{\"name\":\"CONTACT_TOKEN\",\"content\":\"test-contact-token\"},{\"name\":\"VETEMAIL\",\"content\":\"test-vet@getpawprint.com\"}],\"subject\":\"Veterinary Care Reminder from Test Vet\"}",
            "message_type": "reminder",
            "destination": "demo2@getpawprint.com",
            "first_name": "Demo",
            "last_name": "Dee",
            "vet_user_id": 10369
        },
        {
            "id": 55486,
            "type": "sms",
            "created_at": "2017-11-01T00:00:00.000Z",
            "message_content": "Tobi is overdue for a visit on 10/31/2017. Please call 555-555-1234 or email test-vet@getpawprint.com to make an appt with Test Vet",
            "message_type": "reminder",
            "destination": "555-555-6174",
            "first_name": "Demo",
            "last_name": "Dee",
            "vet_user_id": 10369
        }
    ],
    "total": "2"
}
```

Gets all reminder deliveries (`delivery.message_type = reminder`), regardless of type (`email` or `sms`)

### HTTP Request
`GET /partners/delivery`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
order_by | string? | Corresponds to a database column; defaults to `created_at`
dir | string? | `asc` or `desc`; defaults to `desc`
limit | int? | Number of results to return
offset | int? | Start index
filter | string? | Filter by last name (case insensitive)

## Get sent emails and SMSes for reminders and confirmations

> Response example

```json
{
    "data": [
        {
            "id": 55485,
            "type": "email",
            "created_at": "2017-11-01T00:00:00.000Z",
            "message_content": "{\"recipient\":[{\"email\":\"demo2@getpawprint.com\"}],\"from\":{\"name\":\"Test Vet via Pawprint\",\"email\":\"test-vet@getpawprint.com\"},\"variables\":[{\"name\":\"OWNER\",\"content\":\"Demo Dee\"},{\"name\":\"VACCINES\",\"content\":\"<li>Chip's Heartworm Test <b>is due in 1 month</b> (11/30/2017)<li>Chip's K9 Distemper Parvo Vn Titer <b>is due in 1 month</b> (11/30/2017)\"},{\"name\":\"VETBANNER\",\"content\":\"http://www.getpawprint.com/images2/logo@2x.png\"},{\"name\":\"VETNAME\",\"content\":\"Test Vet\"},{\"name\":\"VETAPPOINTMENTWEBSITE\",\"content\":\"http://getpawprint.com\"},{\"name\":\"VETCONTACTCTA\",\"content\":\"Please call 555-555-1234 or email support@getpawprint.com\"},{\"name\":\"VETSIGNATURE\",\"content\":\"Test Vet\"},{\"name\":\"VETPHONE\",\"content\":null},{\"name\":\"CONTACT_TOKEN\",\"content\":\"test-contact-token\"},{\"name\":\"VETEMAIL\",\"content\":\"test-vet@getpawprint.com\"}],\"subject\":\"Veterinary Care Reminder from Test Vet\"}",
            "message_type": "reminder",
            "destination": "demo2@getpawprint.com",
            "first_name": "Demo",
            "last_name": "Dee",
            "vet_user_id": 10369
        },
        {
            "id": 55486,
            "type": "sms",
            "created_at": "2017-11-01T00:00:00.000Z",
            "message_content": "Tobi is overdue for a visit on 10/31/2017. Please call 555-555-1234 or email test-vet@getpawprint.com to make an appt with Test Vet",
            "message_type": "reminder",
            "destination": "555-555-6174",
            "first_name": "Demo",
            "last_name": "Dee",
            "vet_user_id": 10369
        }
    ],
    "total": "2"
}
```
Same as above (`GET /partners/delivery`), but also includes `delivery.message_type = confirmation`.

### HTTP Request
`GET /partners/delivery/vet_history`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
order_by | string? | Corresponds to a database column; defaults to `created_at`
dir | string? | `asc` or `desc`; defaults to `desc`
limit | int? | Number of results to return
offset | int? | Start index
filter | string? | Filter by last name (case insensitive)

## Get reminder cadence

> Response example

```json
{
    "vet_integration_id": 999,
    "cadence": [
        {
            "id": 55,
            "sms": null,
            "email": null,
            "push": true,
            "delta": {
                "days": -1
            },
            "vet_integration_id": 999,
            "created_at": "2018-06-11T22:27:31.609Z",
            "updated_at": "2018-06-11T22:27:31.609Z",
            "postcard": false
        },
        {
            "id": 1,
            "sms": true,
            "email": true,
            "push": true,
            "delta": {
                "days": 60
            },
            "vet_integration_id": 999,
            "created_at": "2017-09-19T22:21:17.482Z",
            "updated_at": "2017-09-19T22:21:17.482Z",
            "postcard": false
        }
    ]
}
```

Gets reminder cadence.

### HTTP Request
`GET /partners/reminder_cadence`

## Create reminder cadence

> Request example

```json
{
  "vet_integration_id": 999,
  "sms": false,
  "email": true,
  "push": true,
  "delta": {
      "days": -1
  },
  "vet_integration_id": 999
}
```

> Response example

```json
{
  "cadence": 55
}
```

Creates a reminder cadence (yes, this is PUT).

### HTTP Request
`PUT /partners/reminder_cadence`

## Update reminder cadence

> Request example

```json
{
  "id": 55,
  "vet_integration_id": 999,
  "sms": false,
  "email": true,
  "push": true,
  "delta": {
      "days": -1
  },
  "vet_integration_id": 999
}
```

> Response example

```json
{
  "cadence": 55
}
```

Updates an existing reminder cadence (yes, this is POST).

### HTTP Request
`POST /partners/reminder_cadence`

## Delete reminder cadence

> Response example

```json
(none)
```

Deletes a reminder cadence.

### HTTP Request
`DELETE /partners/reminder_cadence/:cadence_id`

## Send SMS to user

> Request example

```json
{
  "vet_user_id": 84159,
  "vet_user_phone": "+15555551212",
  "message": "Don't be late!"
}
```

> Response example

```json
(none)
```

Sends an SMS to the `vet_user` and phone number.

### HTTP Request
`POST /partners/sms/send`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_id | integer | `vet_user.id` column (for logging)
vet_user_phone | string | User's phone number - in case it's different from the phone number on file
message | string | Message text

## Get SMS history with a user

> Response example

```json
{
    "history": []
}
```

Gets full chat history between vet and vet_user_id as a union of 2-way text messages and Pawprint-originated messages

### HTTP Request
`GET /partners/sms/history/all/:vet_user_id`

## Get SMS history with a phone number

> Response example

```json
{
    "history": [
        {
            "id": 52202,
            "direction": "inbound",
            "automated": true,
            "message_body": "Hi, I've just arrived in a white BMW",
            "twilio_status": "received",
            "timestamp": "2020-06-28T05:13:32Z",
            "read": true
        },
        {
            "id": 52200,
            "direction": "outbound",
            "automated": false,
            "message_body": "Are you here yet?",
            "twilio_status": "delivered",
            "timestamp": "2020-06-28T05:08:11Z",
            "read": true
        },
    ]
}
```

Gets full chat history between vet and any phone number as a union of 2-way text messages and Pawprint-originated messages

### HTTP Request
`GET /partners/sms/history`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_phone | string | Client's phone number


## Get SMS history with a phone number V2

> Response example

```json
{
    "history": [
        {
            "source": "+14255554321",
            "destination": "+14255551234",
            "direction": "inbound",
            "automated": false,
            "message_body": "Hi, I've just arrived in a white BMW",
            "status": "received",
            "timestamp": "2020-06-28T05:13:32Z",
            "read": true
        },
        {
            "source": "+14255551234",
            "destination": "+14255554321",
            "direction": "outbound",
            "automated": false,
            "message_body": "Are you here yet?",
            "status": "delivered",
            "timestamp": "2020-06-28T05:08:11Z",
            "read": true
        }
    ]
}
```

Gets full chat history between the vet and any phone number as a union of 2-way text messages and automated texts.

Because pagination is affected by active conversations, this endpoint tries to return `$top` items before the `$max_timestamp` parameter (not including `$max_timestamp` itself), depending on how many messages have the exact same timestamp.

An empty array for `history` indicates that the very beginning of SMS history has been reached and no more calls need to be made.

direction | description
--------- | -----------
`inbound` | Written by a human client
`outbound` | Written by a human vet
`automated_outbound` | Generated by the backend, e.g. an appointment confirmation or intake link
`text_command` | Written by a human client; backend parsed it and took an action, e.g. an appointment cancellation

Twilio statuses: `sending...`, `sent`, `delivered`, `undelivered`, `received`

### HTTP Request
`GET /partners/sms/:phone`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | integer? | Number of texts to return; default is 25.
$max_timestamp | datetime? |  Returns texts earlier than, but not including the timestamp; if unspecified, the most recent texts are returned.

## Get all communication history with a vet_user

> Response example

```json
[
    {
        "channel": "sms",
        "source": "+14255554321",
        "destination": "+14255551234",
        "direction": "inbound",
        "automated": false,
        "message_body": "Hi, I've just arrived in a white BMW",
        "twilio_status": "received",
        "status": "received",
        "timestamp": "2020-06-28T05:13:32Z"
    },
    {
        "channel": "sms",
        "source": "+14255551234",
        "destination": "+14255554321",
        "direction": "outbound",
        "automated": false,
        "message_body": "Are you here yet?",
        "status": "delivered",
        "timestamp": "2020-06-28T05:08:11Z"
    },
    {
        "channel": "email",
        "source": null,
        "destination": "demo2@scoutvet.com",
        "direction": "outbound",
        "automated": true,
        "message_body": "Sent intake form for an upcoming appointment",
        "status": "sent",
        "timestamp": "2020-06-28T05:08:11Z"
    }
]
```

Gets all communication history between the vet and client, including backend-generated messages.

Because pagination is affected by active conversations, this endpoint tries to return `$top` items before the `$max_timestamp` parameter (not including `$max_timestamp` itself), depending on how many messages have the exact same timestamp.

An empty array indicates that the very beginning of communication history has been reached and no more calls need to be made.

Channels: `email` or `sms`

direction | description
--------- | -----------
`inbound` | Written by a human client. Currently applies to SMS only.
`outbound` | Written by a human vet. Currently applies to SMS only.
`automated_outbound` | Generated by the backend, e.g. an appointment confirmation or intake link. All emails currently have this status.
`text_command` | Written by a human client; backend parsed it and took an action, e.g. an appointment cancellation. Currently applies to SMS only.

Twilio statuses: `sending...`, `sent`, `delivered`, `undelivered`, `received`

### HTTP Request
`GET /partners/clients/:vet_user_id/communications`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top | integer? | Number of texts to return; default is 25.
$max_timestamp | datetime? |  Returns texts earlier than, but not including the timestamp; if unspecified, the most recent texts are returned.

## Get count of unread SMSes

> Response example

```json
{
  "unknown": 0,
  "existing": 0
}
```

Returns count of unread text messages from unknown phone numbers and phone numbers associated with a `vet_user`.

### HTTP Request
`GET /partners/sms/unread`

## Mark SMS as read
> Request example

```json
{
	"vet_user_id": 84159,
	"vet_user_phone": "+15555551212"
}
```

> Response example

```json
(none)
```

Sets all SMSes as "read" for the given vet user ID and phone number.

### HTTP Request
`POST /partners/sms/mark_read`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vet_user_id | integer | Value from `vet_user.id` column
vet_user_phone | string | User's phone number - in case it's different from the phone number on file

## Get SMSes from a specific unknown phone number

> Response example

```json
{
    "messages": [
      {
        "vet_user_phone": "+15555551212",
        "message_body": "I'd like to set up an appointment for next week",
        "created_at": "2020-06-20T15:32:32Z"
      }
    ]
}
```

Returns SMS messages from the given phone number that doesn't match any phone number in the `vet_user` or `vet_user_phone` tables.

### HTTP Request
`GET /partners/sms/unknown/:phone`

## Get list of unknown phone numbers with messages

> Response example

```json
{
    "clients": [
        {
            "vet_user_phone": "+15555551212",
            "last_contacted": "2020-06-16T12:15:37.000Z",
            "first_contacted": "2019-06-17T21:50:09.340Z"
        }
    ]
}
```

Returns list of clients, first by users with unread messages, then the rest of the clients sorted by last contacted

### HTTP Request
`GET /partners/sms/unknown`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
limit | int? | Number of results to return
offset | int? | Start index