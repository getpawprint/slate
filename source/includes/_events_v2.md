# Events V2
Use these as a template by copying and pasting them.

## Get pet events

> Response example

```json
{
  pagination: {

  },
  events: [

  ]
}
```

Returns pet events in reverse chronological order (newest first), with optional filters applied.

#### Pagination
Use the `$skip` and `$top` parameters. V1 events used `since_time` as the index parameter,
but in that scheme, multiple events with the exact same timestamp would cause more than `$top` results to be returned.

#### Description of response fields

- `id`
- `type` - Event type; foreign key ref into the `eventtype` table.
- `creator` - ID of the user who created the event.
- `timestamp`
- `show_time` - Display flag; there are certain events where the time of day is significant and should be displayed (e.g. went for a walk), and other events where it is not (e.g. date of adoption).
- `status` - Display text; a short summary of the event written by the user or a Pawprint admin.
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
`GET /user/v2/pet/:userid/events`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
event_type | string? | Filters by `event.type` (see `eventtype` table in the database)
$skip | int? | 0-based beginning index of events. Defaults to 0.
$top | int? | Limits number of events in result. Defaults to 25.
