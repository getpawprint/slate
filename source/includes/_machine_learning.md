# Machine learning/algo tagging
For demo pages and machine learning feedback. The state of the machine learning model must be explicitly saved;
if the virtual machine/container which hosts the machine learning service (https://173.255.119.25) is reset, then model changes after
the last save will be lost.

## Query algorithm

> Response example

```json
{
    "type": "reminder_event",
    "id": 23,
    "text": "Parvovirus",
    "score": 0.654672550128199
}
```

Queries the machine learning model with free text. The response contains the table name (`reminder_event` or `medication`), ID and name
of the most probable match, and returns the probability (between 0 and 1) in the `score` field. In the response example,
"Parvovirus" is the best match with a 65.46% probability.

### HTTP Request
`GET /ml/query`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
q | string | Input string for the algorithm.

## Get pending feedback

> Response example

```json
[
    {
        "query": "prom",
        "id": 1,
        "type": "medication",
        "vocab_string": "PromAce, Aceproject"
    },
    {
        "query": "rabies annual",
        "id": 24,
        "type": "reminder_event",
        "vocab_string": "Rabies"
    }
]
```

Gets all pending feedback for the machine learning algorithm that was submitted from vet portals in a list.
This will return an empty list if a refit (see below) was run recently.

### HTTP Request
`GET /ml/feedback`

## Submit feedback
> Request example

```json
{
	"query": "rabies annual",
	"type":"reminder_event",
	"id": 24,
	"is_correct": false
}
```

> Response example

```json
(none)
```

Add a training example to the machine learning feedback table. These are eventually fed back
into the training algorithm to produce a new prediction model.

If you called `GET /ml/query` and the result was **correct**, then you would call `POST /vet/ml/feedback` with
`is_correct: true`, and the `type` and `id` from the result. This gives positive reinforcement for the query.

If the result was **incorrect** , then you would call `POST /vet/ml/feedback` with `is_correct: false`, and the
`type` and `id` of the correct entry, e.g. (`type: reminder_event`, `id: 24`, which corresponds to rabies.) The next
time the training algorithm is run, the new model should not return that incorrect result again.

To get the correct entry, you can call `GET /user/medications` to get a list of all medications and `GET /user/reminder_events`
to get a list of all reminder_events.

<aside class="notice">
Must be logged in as a vet to call this. That is, the auth token must be for a user ID in pawprint_vet.user_id.
</aside>

### HTTP Request
`POST /vet/ml/feedback`

## Delete pending feedback item

> Response example

```json
(none)
```

Deletes a pending feedback item. Expected to be called from the ML refitting page in the admin dashboard.

### HTTP Request
`DELETE /admin/ml/feedback/:feedback_id`

## Refit
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Refits all pending feedback items into the machine learning model.

<aside class="notice">
Must be logged in as an admin to call this. That is, the auth token must be for a user ID in user where type = 0.
</aside>

### HTTP Request
`POST /admin/ml/refit`

## Save state
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Saves the state of the machine learning model to Google Cloud Storage. If the virtual machine or container hosting the machine learning service
is reset, the state of the machine learning model will be restored.

<aside class="notice">
Must be logged in as an admin to call this. That is, the auth token must be for a user ID in user where type = 0.
</aside>

### HTTP Request
`POST /admin/ml/save`

## Get a page of reminders

> Response example

```json
[
    {
        "text": "Exam- Wellness",
        "count": 127333,
    },
    {
        "text": "Vaccination-Rabies",
        "count": 99835,
    }
]
```

Gets a page of text from the vet_reminder table that hasn't had feedback yet, ordered by frequency (most frequently seen strings will be at the top)
then by alphabetical order.

### HTTP Request
`GET /admin/ml/reminders`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
skip | int | Page offset
top | int | Page size

## Get a page of invoices

> Response example

```json
[
    {
        "text": "Exam- Wellness",
        "count": 127333,
    },
    {
        "text": "Vaccination-Rabies",
        "count": 99835,
    }
]
```

Gets a page of text from the vet_invoice table that hasn't had feedback yet, ordered by frequency (most frequently seen strings will be at the top)
then by alphabetical order.

### HTTP Request
`GET /admin/ml/invoices`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
skip | int | Page offset
top | int | Page size
