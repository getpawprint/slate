# Vet portal communications
Communication with users via 2-way text and automated messages

## List clients by last incoming SMS date

> Response example

```json
[
    {
        "id": 84159,
        "email": "echoi@scoutvet.com",
        "phone": "(555) 555-1234",
        "first_name": "Eric",
        "last_name": "Choi",
        "pets": [
            {
                "name": "Glumpy",
                "deceased": false
            },
            {
                "name": "Pumpkin",
                "deceased": true
            },
        ]
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
last contact date (most recent first), and with the latest SMS to or from that client.
Phone numbers not associated with a `vet_user` will have a null `id` and only a phone number.
Currently does not include clients that were created in the ScoutVet database via standalone, Vetstoria or vet portal intakes.

### HTTP Request
`GET /partners/clients`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
$top| int? | Number of results to return; defaults to 10
filter | string? | Filter by first name or last name or phone number (case insensitive)

## Get SMS history with a phone number V2

> Response example

```json
[
    {
        "id": 45,
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
        "id": 44,
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
```

Gets full chat history between the vet and any phone number as a union of 2-way text messages and automated texts.

Because pagination is affected by active conversations, this endpoint tries to return `$top` items before the `$max_timestamp` parameter (not including `$max_timestamp` itself), depending on how many messages have the exact same timestamp.

An empty array indicates that the very beginning of SMS history has been reached and no more calls need to be made.

Directions: `inbound`, `outbound`

Automated: boolean

Statuses: `sending...`, `sent`, `delivered`, `undelivered`, `received`

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
        "id": 45,
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
        "id": 44,
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
        "id": 10090,
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

Directions: `inbound`, `outbound`

Automated: boolean

Statuses: `sending...`, `sent`, `delivered`, `undelivered`, `received`

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