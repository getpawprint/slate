# Medications
Medications - both built-in and user-added medications.

## Get all built-in medications

> Response example

```json
[
	{
		"id": 485,
		"name": "Seresto",
		"description": "Flea/tick collar",
		"active_ingredient": "imidacloprid, flumethrin"
	},
	{
		"id": 284,
		"name": "ProHeart 6",
		"description": "Heartworm preventative",
		"active_ingredient": "moxidectin"
	},
	{
		"id": 479,
		"name": "Scalibor",
		"description": "Flea/tick collar",
		"active_ingredient": "flumethrin, imidacloprid"
	},
	{
		"id": 483,
		"name": "Sentry Flea/Tick Collar",
		"description": "Flea/tick collar",
		"active_ingredient": "deltamethrin"
	}
]
```

Gets all medications built in with Pawprint.

### HTTP Request
`GET /user/medications`

## Get all of a pet's medications

> Response example

```json
[
		{
				"medication_id": 464,
				"id": 3523,
				"pet_id": 68795,
				"dosage_number": 1,
				"dosage_unit": "tablet",
				"times": null,
				"frequency": "weekly",
				"notes": null,
				"name": "Heartgard Plus",
				"reminder_id": null,
				"description": "Heartworm/intestinal worm preventative",
				"active_ingredient": "ivermectin, pyrantel pamoate",
				"mode": "oral",
				"warning": null,
				"prescription": true,
				"title": null,
				"start_date": null,
				"until_date": null,
				"frequency_count": null,
				"frequency_unit": null,
				"type": null,
				"schedule": null,
				"marked_done": null
		},
		{
				"medication_id": 9,
				"id": 4040,
				"pet_id": 68795,
				"dosage_number": 1,
				"dosage_unit": "tablet",
				"times": null,
				"frequency": null,
				"notes": "For anxiety",
				"name": "Xanax",
				"reminder_id": 14182,
				"description": "Sedative",
				"active_ingredient": "alprazolam",
				"mode": "oral",
				"warning": null,
				"prescription": true,
				"title": null,
				"start_date": null,
				"until_date": null,
				"frequency_count": null,
				"frequency_unit": null,
				"type": null,
				"schedule": null,
				"marked_done": null,
		}
]
```

Gets a medication for a pet. Medications come from the `pet_medication` table.

, in which case the `source` field will have the value `pet_medication`,
or the `inferred_medication_raw` table, in which case the `source` field will have the value `inferred`. Inferred medications come from the output of a
natural language processing algorithm run over the `vet_pet_history`, `vet_reminder` and `vet_invoice` tables, so they cannot be deleted.

### HTTP Request
`GET /user/v2/pets/:petid/medications/`

## Create pet medication
> Request example

```json
{
	"dosage_number": 1,
	"dosage_unit": "tablet",
	"notes": "For anxiety",
	"name": "Xanax",
	"medication_id": 9,
	"reminder": {
		"start_date": "2018-01-25T13:00:00-08:00",
		"until_date": "2019-01-25T13:00:00-08:00",
		"frequency_unit": "daily",
		"frequency_count": 1,
		"schedule": null,
		"title": "Xanax",
		"notes": "I'm a medication reminder",
	}
}
```

> Response example

```json
(none)
```

Create a pet medication. The `reminder` field is optional and follows the format of creating a V2 reminder, except
pet_id and type do not need to be specified.

### HTTP Request
`POST /user/v2/pets/:petid/medication`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
dosage_number | int? | Size of a dose of the medication
dosage_unit | string? | Units for the dose
notes | string? | Notes
name | string | Name of the medication
medication_id | int? | Foreign key into the `medication` table; null if the medication doesn't exist in our system
reminder | object | Reminder object; see documentation for Reminders V2 - Create a reminder
times | int? | deprecated; is stored in database but has no effect.
frequency | string? | deprecated; is stored in database but has no effect.

## Update pet medication
> Request example

```json
{
	"dosage_number": 1,
	"dosage_unit": "tablet",
	"notes": "For anxiety",
	"name": "Xanax",
	"medication_id": 9,
	"reminder": {
		"start_date": "2018-01-25T13:00:00-08:00",
		"until_date": "2019-01-25T13:00:00-08:00",
		"frequency_unit": "daily",
		"frequency_count": 1,
		"schedule": null,
		"title": "Afoxolaner",
		"notes": "I'm a medication reminder",
	}
}
```

> Response example

```json
(none)
```

Updates a pet medication.
`:petid` is an ID from the `pet` table, while `:medicationid` is an ID from the `pet_medication` table.
If a `reminder` object is supplied, either the existing reminder is updated or a new reminder is created.
If a `reminder` object is not supplied in the request, but there is an existing reminder for that medication,
the reminder will be deleted.


### HTTP Request
`PUT /user/v2/pets/:petid/medication/:medicationid`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
dosage_number | int? | Size of a dose of the medication
dosage_unit | string? | Units for the dose
notes | string? | Notes
name | string | Name of the medication
medication_id | int? | Foreign key into the `medication` table; null if the medication doesn't exist in our system
reminder | object | Reminder object; see documentation for Reminders V2 - Create a reminder
times | int? | deprecated; is stored in database but has no effect.
frequency | string? | deprecated; is stored in database but has no effect.


## Delete pet medication
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Deletes a pet medication.
`:petid` is an ID from the `pet` table, while `:medicationid` is an ID from the `pet_medication` table.

### HTTP Request
`DELETE /user/v2/pets/:petid/medication/:medicationid`

## Get suggested pet medications

> Response example

```json
[
    {
        "id": 8161,
        "medication_id": 9,
        "pet_id": 67543,
        "name": "Alprazolam (Xanax®)",
        "description": "Sedative",
        "active_ingredient": "alprazolam",
        "notes": "From vet"
    }
]
```

Gets suggested medications for a pet. These suggestions come from running algo tagging on the `vet_pet_history`, `vet_reminder` and `vet_invoice` tables.
Users should be able to accept or reject these suggestions - accepting will go to the "Add Medication" page with the
fields pre-populated by this API, and then a new medication will be added to the `pet_medication` table.
Medications already in the `pet_medication` table will not appear in the suggested medications queue.

If the suggestion is rejected, then the medication will not appear in the queue again unless there is more vet data
at a later date which suggests the same medication.

### HTTP Request
`GET /user/v2/pets/:petid/medication/suggested`

## Reject suggested pet medication

> Response example

```json
(none)
```

> Response example

```json
(none)
```

Rejects a suggested medication from the queue. The medication will not appear in the queue again unless there is more vet data
at a later date which suggests the same medication.

### HTTP Request
`DELETE /user/v2/pets/:petid/medication/suggested/:suggestionid`



## Get medications for a pet medication (admin)

> Response example

```json
[
	{
		"id": 6060,
		"pet_id": 104306,
		"dosage_number": null,
		"dosage_unit": null,
		"name": "ProHeart 6",
		"times": null,
		"frequency": null,
		"notes": null,
		"medication_id": 284,
		"reminder_id": null,
		"verified": true,
		"date_prescribed": "2018-10-03"
	}
]
```

Gets only verified medications for a pet. Pawprint admins can only view, add, edit or delete verified medications for a pet, while users can only view verified medications. The `times` and `frequency` fields are deprecated (should be ignored) and `reminder_id` is relevant to users (see user-space documentation above for more info). 

### HTTP Request
`GET /admin/pets/:petid/medications`


## Add pet medication (admin)

> Request example

```json
{
	"medication_id": 9,
	"dosage_number": 1,
	"dosage_unit": "tablet",
	"notes": "For anxiety",
	"date_prescribed": "2018-10-03"
}
```

> Response example

```json
{ "id": 606 }
```

Adds a verified medication for the given pet. Pawprint admins can only view, add, edit or delete verified medications for a pet, while users can only view verified medications.

### HTTP Request
`POST /admin/pets/:petid/medications`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
medication_id | int | Foreign key into the `medication` table; null if the medication doesn't exist in our system
dosage_number | int? | Size of a dose of the medication
dosage_unit | string? | Units for the dose
notes | string? | Notes
date_prescribed | string? | Date the medication was prescribed (no time)

## Update pet medication (admin)

> Request example

```json
{
	"medication_id": 9,
	"dosage_number": 1,
	"dosage_unit": "tablet",
	"notes": "For anxiety",
	"date_prescribed": "2018-10-03"
}
```

> Response example

```json
(none)
```

Updates a verified medication for the given pet. Pawprint admins can only view, add, edit or delete verified medications for a pet, while users can only view verified medications.

### HTTP Request
`PUT /admin/pets/medications/:pet_medication_id`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
medication_id | int | Foreign key into the `medication` table; null if the medication doesn't exist in our system
dosage_number | int? | Size of a dose of the medication
dosage_unit | string? | Units for the dose
notes | string? | Notes
date_prescribed | string? | Date the medication was prescribed (no time)

## Delete pet medication (admin)

Deletes a verified pet medication. Pawprint admins can only view, add, edit or delete verified medications for a pet, while users can only view verified medications.

### HTTP Request
`DELETE /admin/pets/medications/:pet_medication_id`

