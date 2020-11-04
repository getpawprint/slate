# Form Library
Endpoints for vets to list, create and update forms.

## Get list of template forms

> Response example

```json
[
  {
    "id": 10,
    "name": "Surgery"
  },
  {
    "id": 11,
    "name": "Boarding/dropoff"
  },
  {
    "id": 12,
    "name": "Estimates"
  }
]
```

Gets a list of form templates. These are form bases provided by Snout; no instance should use these directly.

### HTTP Request
`GET /partners/templates`

## Get list of vet's active forms

> Response example

```json
[
  {
    "id": 510,
    "name": "Jacksonville surgery",
    "appointment_types": ["surgery", "dental"],
    "created_at": "2020-08-08"
  } 
]
```

Gets a list of forms, ordered by creation date in chronological order. If a form is edited, its ID in this list gets replaced with the "new" one, while the previous version is frozen.

### HTTP Request
`GET /partners/forms`

## Get a specific form or template

> Response example

```json
{
  "appointment_types": ["surgery", "dental"],
  "template_id": 10,
  "form": {
    "name": "Jacksonville Surgery",
    "per": "pet",
    "modules": [
      {
        "label": "Description",
        "is_visible": true,
        "questions": [{
          "name": "label1",
          "label": "Vivamus sagittis lacus vel augue laoreet rutrum faucibu.",
          "input_type": "label",
          "required": false
        }],
      },
      {
        "label": "List Menu",
        "is_visible": true,
        "questions": [{
          "name": "allergies",
          "label": "Lorem Ipsum Menu Title",
          "input_type": "checkbox",
          "options": ["Option 1", "Option 2", "Option 3", "Option 4"],
          "filled_by": "client",
          "required": false
        }]
      },
      {
        "label": "Single-option selector",
        "is_visible": false,
        "questions": [{
          "name": "Lorem Ipsum",
          "label": "Cras mattis consectetur purus sit amet fermentum.",
          "input_type": "radio",
          "options": ["Accept", "Decline"],
          "filled_by": "client",
          "required": false
        }]
      },
      {
        "label": "Single-option selector",
        "is_visible": true,
        "questions": [{
          "name": "Lorem Ipsum",
          "label": "Cras mattis consectetur purus sit amet fermentum.",
          "input_type": "radio",
          "options": ["Accept", "Decline"],
          "filled_by": "vet",
          "required": true
        }]
      },
      {
        "label": "Signature",
        "is_visible": true,
        "questions": [{
          "name": "Lorem Ipsum",
          "label": "Cras mattis consectetur purus sit amet fermentum.",
          "input_type": "signature",
          "filled_by": "client",
          "required": true
        },
        {
          "name": "Name",
          "label": null,
          "input_type": "text",
          "filled_by": "client",
          "required": true
        },
        {
          "name": "Date",
          "label": null,
          "input_type": "date",
          "required": false
        }]
      }
    ]
  }
}
```

Returns a form. The form name can be `null` if the `form_id` parameter is for a form template.

### HTTP Request
`GET /partners/forms/:form_id`

## Create a new form

> Request example

```json
{
  "appointment_types": ["surgery", "dental"],
  "template_id": 10,
  "previous_id": 510,
  "form": {
    "name": "Surgery",
    "per": "pet",
    "modules": [
      {
        "label": "Description",
        "is_visible": true,
        "questions": [{
          "name": "label1",
          "label": "Vivamus sagittis lacus vel augue laoreet rutrum faucibu.",
          "input_type": "label",
          "required": false
        }],
      },
      {
        "label": "List Menu",
        "is_visible": true,
        "questions": [{
          "name": "allergies",
          "label": "Lorem Ipsum Menu Title",
          "input_type": "checkbox",
          "options": ["Option 1", "Option 2", "Option 3", "Option 4"],
          "filled_by": "client",
          "required": false
        }]
      },
      {
        "label": "Single-option selector",
        "is_visible": false,
        "questions": [{
          "name": "Lorem Ipsum",
          "label": "Cras mattis consectetur purus sit amet fermentum.",
          "input_type": "radio",
          "options": ["Accept", "Decline"],
          "filled_by": "vet"
        }]
      },
      {
        "label": "Single-option selector",
        "is_visible": true,
        "questions": [{
          "name": "Lorem Ipsum",
          "label": "Cras mattis consectetur purus sit amet fermentum.",
          "input_type": "radio",
          "options": ["Accept", "Decline"],
          "filled_by": "client",
          "required": true
        }]
      },
      {
        "label": "Signature",
        "is_visible": true,
        "questions": [{
          "name": "Lorem Ipsum",
          "label": "Cras mattis consectetur purus sit amet fermentum.",
          "input_type": "signature",
          "filled_by": "client",
          "required": true
        },
        {
          "name": "Name",
          "label": null,
          "input_type": "text",
          "filled_by": "client",
          "required": true
        },
        {
          "name": "Date",
          "label": null,
          "input_type": "date",
          "required": false
        }]
      }
    ]
  }
}
```

> Response example

```json
{
  "id": 511
}
```

Creates a form. If `previous_id` is specified, that form is taken out of the vet's list of active forms and replaced with the newly created form instead.

### HTTP Request
`POST /partners/forms`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
appointment_types | string[] | Datasync intakes only - an instance of this form is generated on appointment type match.
template_id | int | Form template ID
previous_id | int? | Form ID; if specified, the previous form will be deactivated and this form will be activated to simulate an "edit" while preserving the form history for previous instances.
form | object | Form specification
form.name | string | Form name (human-friendly string)
form.per | string | One of `user` or `pet`
form.modules | object[] | Form modules, which may contain multiple questions
form.modules.label | string | Section name (human-friendly string)
form.modules.is_visible | boolean | Whether or not this section is visible
form.modules.questions | object[] | Questions that appear in this section
form.modules.questions.name | string | Internal name for data binding
form.modules.question.label | string | Human-friendly string
form.modules.question.input_type | string | One of `label` (not required to be answered), `text`, `signature` (answer is a base64 image), `date` (no time), `radio` or `checkbox`
form.modules.question.options | string[] | Options for `radio` or `checkbox` input types only
form.modules.question.filled_by | string? | One of `client` or `vet`
form.modules.question.required | boolean | Whether or not a response is required for this question

## Get list of appointment types

> Response example

```json
[
  "Vaccinations",
  "New Kitten",
  "New Puppy",
  "Wellness Exam",
  "Dental",
  "Neuter",
  "Spay",
  "Surgery",
  "Euthanasia"
]
```

Gets list of vet's appointment types found in `vet_schedule.type`, ordered by most to least frequently used.
If the vet does not have datasync enabled, then this will return HTTP 204/No Content.

### HTTP Request
`GET /partners/appointment_types`

## Get price estimate groups

> Response example

```json
[
  {
    group: 'Feline Spay',
    items: [
      {
        item: 'Anesthesia - Inhalant - First 30 Mins',
        unit_price: '77.57',
        quantity: {
          min: 1,
          max: null,
        },
      },
    ]
  }
]
```

Gets line items for treatment estimates, usually for surgery. Vets' cost estimates for a procedure (like spay/neuter)
are commonly comprised of a group of line items. Estimates come from the database table `place_estimate`.

### HTTP Request
`GET /partners/estimates`