# Form Instances
Endpoints for vets to attach, remove, and partially fill out forms.

## Attach form to intake
> Request example

```json
{
  "form_id": 510
}
```

> Response example

```json
{
  "form_instance_id": 52
}
```

Creates an instance of a form an attaches it to an intake. Its initial state will be `pending vet` or `pending user`. Returns a form instance ID that can be used to update or cancel a form instance. A notification is sent to the user, prompting them to fill out the form.

### HTTP Request
`POST /partners/intake/:intake_id/form_instances`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
form_id | integer | Form ID (from the form library)


## Cancel a form from an intake
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Changes the state of a form instance to `cancelled`. Form instances cannot be removed from intakes.

### HTTP Request
`DELETE /partners/form_instances/:form_instance_id`

## Fill in a form instance (vet)
> Request example

```json
{
  "allergies": ["Beef","Dairy"],    // checkboxes
  "my_radio": "Accept",    // radio
  "procedure": "Dental cleaning",   // text
  "signature": "data:image/png;base64,iVBORw0KGgoAAARK5CYII=",  // signature
  "name": "John Smith",    // text
}
```

> Response example

```json
(none)
```

Fills in a form on the vet side. The form must be in the `pending vet` state, and filling in all the vet-required fields (denoted by `question.filled_by`) will automatically advance the form_instance state to `pending user`.

### HTTP Request
`PATCH /partners/form_instances/:form_instance_id`

### PATCH parameters
Object keys should match the `question.name` fields and object values should be appropriate for their control types.

## Fill in a form instance (client)
> Request example

```json
{
  "allergies": ["Beef","Dairy"],    // checkboxes
  "my_radio": "Accept",    // radio
  "procedure": "Dental cleaning",   // text
  "signature": "data:image/png;base64,iVBORw0KGgoAAARK5CYII=",  // signature
  "name": "John Smith",    // text
  "date": "2020-08-28"     // date
}
```

> Response example

```json
(none)
```

Fills in a form on the client side. The form must be in the `pending user` state, and filling in all the client-required fields (denoted by `question.filled_by`) will automatically advance the form_instance state to `complete`.

### HTTP Request
`PATCH /form_instances/:form_instance_external_id`

### PATCH parameters
Object keys should match the `question.name` fields and object values should be appropriate for their control types.

## Get a form instance (vet portal)
> Request example

```json
{
  "status": "pending vet",
  "created_at": "2020-08-22T20:32:14Z",
  "updated_at": "2020-08-22T20:34:34Z",
  "form_instance": {
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
          "response": null
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
          "response": null
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
          "response": null
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
          "response": "Accept"
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
          "response": null
        },
        {
          "name": "Name",
          "label": null,
          "input_type": "text",
          "filled_by": "client",
          "response": null
        },
        {
          "name": "Date",
          "label": null,
          "input_type": "completed_date",
          "response": null
        }]
      }
    ]
  }
}
```

> Response example

```json
(none)
```

Gets a form instance.

### HTTP Request
`GET /partners/form_instances/:form_instance_id`