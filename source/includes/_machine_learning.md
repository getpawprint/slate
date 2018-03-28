# Machine learning/algo tagging
For demo pages and machine learning feedback.

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

## Feedback
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

**Must be logged in as a vet to call this.**

### HTTP Request
`POST /vet/ml/feedback`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
query | string | Freeform text input string containing the example.
type | string | Either `reminder_event` or `medication`.
id | integer | Either a `reminder_event.id` or `medication.id`.
is_correct | boolean? | Whether or not the original prediction was correct. This isn't fed back to the algo; it is only used for reporting.
