# Events V2
Use these as a template by copying and pasting them.

## Get pet events

> Response example

```json
{
  "paging": {
    "total": 9,
    "start": 0,
    "next": 5
  },
  "events": [
    {
      "id": 18429185,
      "type": 5,
      "creator": 37683,
      "first_name": "Eric",
      "last_name": "Choi",
      "timestamp": "2018-01-08T18:37:07.000Z",
      "show_time": true,
      "status": null,
      "data": {
          "weight": 8
      },
      "verified": false,
      "images": []
    },
    {
      "id": 16260729,
      "type": 1,
      "creator": 37683,
      "first_name": "Eric",
      "last_name": "Choi",
      "timestamp": "2017-08-08T17:12:06.000Z",
      "show_time": false,
      "status": null,
      "data": null,
      "verified": false,
      "images": [],
      "reminder_event": {
          "id": 1,
          "name": "Bordetella",
          "type": "vaccine",
          "primary_condition": "Bordetella"
      },
      "vaccination": {
          "expiry": "2018-08-08T07:00:00.000Z",
          "tagnumber": "43245",
          "expiryStatus": "expired"
      },
      "place": {
        "id": 27015,
        "name": "Test Animal Clinic"
      }
    },
    {
      "id": 16260728,
      "type": 6,
      "creator": 37683,
      "first_name": "Eric",
      "last_name": "Choi",
      "timestamp": "2017-08-08T17:11:13.000Z",
      "show_time": true,
      "status": "Type 2 poo",
      "data": null,
      "verified": false,
      "images": []
    },
    {
      "id": 3839524,
      "type": 10,
      "creator": 51694,
      "timestamp": "2017-03-02T00:00:00.000Z",
      "show_time": false,
      "status": "Bordetella Oral 1 year",
      "verified": true,
      "images": null,
      "place": {
          "id": 27015,
          "name": "Test Animal Clinic"
      }
    },
    {
      "id": 322002,
      "type": 9,
      "creator": 51694,
      "timestamp": "2014-08-11T07:00:00.000Z",
      "show_time": false,
      "status": "Office visit",
      "verified": true,
      "images": null,
      "place": {
          "id": 27015,
          "name": "Test Animal Clinic"
      }
    }
  ]
}
```

Returns pet events in reverse chronological order (newest first), with optional filters applied.

#### Pagination
Use the `$skip` and `$top` parameters. V1 events used `since_time` as the index parameter,
but in that scheme, multiple events with the exact same timestamp would cause more than `$top` results to be returned.
Paging information is in the `paging` object of the response; in particular, `paging.next` should be passed directly as the
`$skip` parameter of the call to get the next page, and paging should be stopped when `paging.next` >= `paging.total`.

#### Description of response fields

- `id`
- `type` - Event type; foreign key ref into the `eventtype` table.
- `creator` - ID of the user who created the event.
- `first_name` - First name of the user who created the event; may be null if the event was linked from vet data.
- `first_name` - Last name of the user who created the event; may be null if the event was linked from vet data.
- `timestamp`
- `show_time` - Display flag; there are certain events where the time of day is significant and should be displayed (e.g. went for a walk), and other events where it is not (e.g. date of adoption).
- `status` - Display text; a short summary of the event written by the user or a Pawprint admin.
- `data` - Right now this only contains the weight (in pounds) for `type` = 5 (weight event).
- `verified` - `true` only for events that were created by a Pawprint admin or vet integration; `false` otherwise.
- `images` - Array of image URLs attached to the event; may be null.
- `place` - Object containing information about where this event occurred; may be null.
  - `id` - ID of the place where the event occurred.
  - `name` - Name of the place where the event occurred.
- `reminder_event` - Object containing information about the condition or procedure associated with this event; may be null.
  - `id` - ID of the `reminder_event` of the event; the `reminder_event` table enumerates a list of vaccines, blood tests, procedures, exams and medications that are known to Pawprint and for which reminders may be set. Reminders may be set by Pawprint admins (as part of a records request) or by users.
  - `name` - The `reminder_event`'s type.
  - `type` - The `reminder_event`'s type; current possible values are `vaccine`, `medication`, `procedure`, `exam`, `lab_blood` and `lab_stool`.
  - `primary_condition` - Primary condition covered by `vaccine` and `lab_blood` reminder event types. For example, "Distemper-parvo" is the primary condition covered by any of "DA2PP", "DA2PPC" or "Durammune Max 5".
- `vaccination` - Object containing information about this specific vaccination; may be null.
  - `expiry` - Vaccine expiration date (if any)
  - `expiryStatus` - `expired` if the vaccination is expired; `coming due` if the vaccination is less than 1 month away from expiration; `active` if the vaccination is not expired or coming due and also not "covered" by a more recent vaccination for the same primary condition; `historic` if the vaccination is still active but "covered" by a more recent vaccination for the same primary condition.
  - `serial` - For certain vaccinations
  - `tagnumber` - For certain vaccinations

### HTTP Request
`GET /user/v2/pet/:pet_id/events`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
event_type | string? | Filters by `event.type` (see `eventtype` table in the database)
$skip | int? | 0-based beginning index of events. Defaults to 0.
$top | int? | Limits number of events in result. Defaults to 25.
