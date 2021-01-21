# Insurance
Get insurance quotes, or existing pet insurance policies

## Get insurance companies

> Response example

```json
[
    {
        "id": 1,
        "name": "24PetWatch",
        "integrated": null,
        "description": null,
        "logo": null
    },
    {
        "id": 2,
        "name": "AKC Pet Healthcare Plan",
        "integrated": null,
        "description": null,
        "logo": null
    }
]
```

Gets the list of all insurance companies known to Pawprint. Public API.

### HTTP Request
`GET /user/insurance`, `GET /insurance`

## Add a pet insurance policy
> Request example

```json
{
  "insurance_id": 1,
	"policy_number": "A10358"
}
```

> Response example

```json
(none)
```

Adds a pet insurance policy.

### HTTP Request
`POST /pets/:petid/insurance`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
policy_number | string | Pet insurance policy number.
insurance_id | int | ID from the 

## Delete a pet's insurance policies

> Response example

```json
(none)
```

Deletes a pet's insurance policies. Typically a pet has only 1.

### HTTP Request
`DELETE /pets/:petid/insurance`