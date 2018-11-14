# Reminders v2
V2 reminders. These can be one-shot reminders, or be scheduled to repeat at specific intervals
and with specified start/end dates. A user can have multiple reminders attached to it.
Timing details are stored in the `custom_reminder_v2` table, while per-user settings
are stored in the `user_reminder_v2` table. All operations on specific reminders
use `user_reminder_v2.id` as the parameter; only the creator can modify a reminder.

## Get all reminders
> Response example

```json
{
    "reminders": [
        {
            "id": 5,
            "reminder_id": 6,
            "created_by": 39654,
            "pet_id": 67555,
            "start_date": "2018-01-25T21:00:00.000Z",
            "until_date": null,
            "marked_done": null,
            "frequency_unit": null,
            "frequency_count": 0,
            "schedule": "{\"schedules\":[{\"dw\":[2,4,6]}],\"exceptions\":[]}",
            "title": "My reminder",
            "notes": "Grain-free only",
            "active": true,
            "source": "user",
            "type": null
        },
        {
            "id": 340,
            "reminder_id": 311,
            "created_by": 39654,
            "pet_id": 67555,
            "start_date": "2018-01-25T21:00:00.000Z",
            "until_date": "2019-01-25T21:00:00.000Z",
            "marked_done": null,
            "frequency_unit": "days",
            "frequency_count": 35,
            "schedule": null,
            "title": "My updated reminder",
            "notes": "z/d only",
            "active": true,
            "source": "user",
            "type": 4
        },
        {
            "id": 134402,
            "reminder_id": 134402,
            "created_by": 37851,
            "pet_id": 67543,
            "start_date": "2019-01-01T08:00:00.000Z",
            "title": "Lepto vaccination",
            "notes": "Palmer Veterinary Clinic",
            "source": "vet_reminder",
            "type": 0,
            "schedule": null,
            "until_date": null,
            "marked_done": null,
            "frequency_unit": null,
            "frequency_count": null,
            "active": false
        }
    ],
    "deleted": []
}
```

Gets all active and non-active reminders for a user. Deleted reminders are omitted.
**This also includes (non-ignored) reminders from the vet_reminder table.**

### HTTP Request
`GET /user/v2/all_reminders`

## Get specific reminder
> Response example

```json
{
    "id": 340,
    "reminder_id": 311,
    "created_by": 39654,
    "pet_id": 67555,
    "start_date": "2018-01-25T21:00:00.000Z",
    "until_date": "2019-01-25T21:00:00.000Z",
    "marked_done": null,
    "frequency_unit": "days",
    "frequency_count": 35,
    "schedule": null,
    "title": "My updated reminder",
    "notes": "z/d only",
    "active": true,
    "source": "user",
    "type": 4
}
```

Gets a specific reminder by `user_reminder_v2.id`.

### HTTP Request
`GET /user/v2/reminder/:userreminderid`

## Create a reminder
> Request example

```json
{
	"pet_id": 67555,
	"start_date": "2018-01-25T13:00:00-08:00",
	"until_date": "2019-01-25T13:00:00-08:00",
	"frequency_unit": null,
	"frequency_count": 0,
	"schedule": "{\"schedules\":[{\"dw\":[7]}],\"exceptions\":[]}",
	"title": "My reminder",
	"notes": "Grain-free only",
	"type": 3
}
```

> Response example

```json
{
    "id": 340
}
```

Returns the `user_reminder_v2.id` of the newly created reminder. Only one of `schedule` or
the tuple (`frequency_unit`, `frequency_count`) should be specified. `schedule` is used
for day-of-week reminders while `frequency_unit` and `frequency_count` are used for everything else.

### HTTP Request
`POST /user/v2/reminder`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
pet_id | int | ID from the `pet` table.
start_date | string | When the first reminder should go off. For time zone reasons, this is a pass-through string.
until_date | string| When the reminder should end. For a one-shot reminder, leave this null and frequency_count=0. For a forever repeating reminder, leave this null and frequency_count>0. For time zone reasons, this is a pass-through string.
frequency_unit | string | Unit of measure for `frequency_count`, e.g. `days`.
frequency_count | int | How often the reminder should repeat; combined with `frequency_unit`.
schedule | string | JSON object parsable by [later.js](https://bunkat.github.io/later/).
title | string | Reminder title.
notes | string | Reminder notes.
type | int | ID from the `eventtype` table.

## Update a reminder
> Request example

```json
{
	"start_date": "2018-01-25T13:00:00-08:00",
	"until_date": "2019-01-25T13:00:00-08:00",
	"frequency_unit": null,
  "frequency_count": 0,
	"schedule": "{\"schedules\":[{\"dw\":[7]}],\"exceptions\":[]}",
	"title": "My reminder",
	"notes": "Grain-free only",
	"type": 3
}
```

> Response example

```json
(none)
```

Updates a reminder. Only the creator can update a reminder; otherwise HTTP 403/Forbidden is returned.

### HTTP Request
`PUT /user/v2/reminder/:userreminderid`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
start_date | string | When the first reminder should go off. For time zone reasons, this is a pass-through string.
until_date | string| When the reminder should end. For a one-shot reminder, leave this null and frequency_count=0. For a forever repeating reminder, leave this null and frequency_count>0. For time zone reasons, this is a pass-through string.
frequency_unit | string | Unit of measure for `frequency_count`, e.g. `days`.
frequency_count | int | How often the reminder should repeat; combined with `frequency_unit`.
schedule | string | JSON object parsable by [later.js](https://bunkat.github.io/later/).
title | string | Reminder title.
notes | string | Reminder notes.
type | int | ID from the `eventtype` table.

## Delete a reminder
(Soft) Deletes a reminder. Only the creator can delete a reminder; otherwise HTTP 403/Forbidden is returned.
To deactivate a reminder, non-creators can use the `/v2/reminder/user/:userreminderid/settings` endpoint.

### HTTP Request
`DELETE /user/v2/reminder/:userreminderid`

## Update settings
> Request example

```json
{
	"active": false,
	"remove": true
}
```

> Response example

```json
(none)
```

Both creators and non-creators can update their reminder settings. One or both parameters can be specified.

### HTTP Request
`PUT /user/v2/reminder/user/:userreminderid/settings`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
active | boolean | Turns a reminder on/off, only for the calling user.
remove | boolean | Soft deletes a reminder, only for the calling user, even if they're not its creator.

## Update settings (vet_reminder)
> Request example

```json
{
	"active": false,
	"done": true
}
```

> Response example

```json
(none)
```

Both marks a `vet_reminder` as ignored or complete from the home page. One or both parameters can be specified.
Effectively, there is no difference in behavior if a reminder was ignored vs. marked as done.

### HTTP Request
`PUT /user/v2/reminder/vet/:vetreminderid/settings`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
active | boolean | Ignores the reminder if set to `true`.
done | boolean | Marks the reminder as done if set to `true`.

