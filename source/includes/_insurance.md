# Insurance claims
API to create an insurance claim. Each claim consists of a user, pet, place, claim-specific information, and one or more invoice documents. The user may create a record request while filling out the claim; the claim will then only be submitted after the record request is complete. If the user opts out of the record request, then the claim will be submitted as soon as the user is finished filling it out.

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

Gets the list of all insurance companies known to Pawprint.

### HTTP Request
`GET /user/insurance`

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


## Create a claim

> Request example

```json
{
  "user_id": 101,
  "pet_id": 2345,
  "place_id": 27015,
  "pet_insurance_id": 66,	
  "visit_type": "wellness exam",	
  "symptoms_start_date": "2018-11-01",	
  "symptoms": [ "Diarrhea", "Vomiting", "Lack of appetite" ],
  "diagnosis": "Gastroenteritis",	
  "is_new_claim": true,	
  "was_claimed_before": false,	
  "should_create_request": true,	
  "signature": "https://s3.aws.amazon.com/pawprint-claims/sig.png",	
  "invoice_number": "6425537",
  "invoice_date": "2018-11-10",
  "invoice_total": 253.87,
  "files": [ 167,	168 ]	
}
```

> Response example

```json
{
  "id": 1345
}
```

Creates a claim, which uses the information to create a row in the database and reserves an ID.

### HTTP Request
`POST /user/claims`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
user_id | int | ID from the `user` table.
pet_id | int| ID from the `pet` table. The user must be the pet's owner (`pet.owner`)...?
place_id | int | The vet where invoices are coming from for this claim.
pet_insurance_id | int | ID from the `pet_insurance` table.
visit_type | string | One of "wellness", "injury", "accident", or... Freeform string?
symptoms_start_date | datetime? or string? | If a datetime is passed, only the date will be stored and the time portion will be truncated. Stored as a timezone-agnostic string in the database.
symptoms | string[] | List of symptoms
diagnosis | string | Freeform string of the vet's diagnosis
is_new_condition | bool | Answer to "This is a new condition"
was_claimed_before | bool | Answer to "I have filed a claim for this condition previously"
should_create_request | bool | Answer to "Request records from my vet for this visit"; will not submit claim until request is complete
signature | string | URL to signature file
invoice_number | string | invoice number; pick one if the user submitted multiple
invoice_date | datetime or string | invoice date; pick one if the user submitted multiple
invoice_total | number | Sum of all invoice amounts
files | integer[] | `file_id`s to attach to the insurance claim. At least one file must be submitted with the claim.

## Get user's existing claims (summary)

> Response example

```json
[
  {
    "id": 10,
    "user_id": 101,
    "pet_id": 2345,
    "place_name": "Pawprint, Inc.",
    "insurance_name": "Pawprint Insurance Co.",
    "policy_number": "A000007",
    "invoice_total": 456.78,
    "status": "submitted",
    "created_at": "2018-12-01",
    "updated_at": "2018-12-01"
  }
]
```

Gets a summary of all of the user's existing insurance claims.

### HTTP Request
`GET /user/claims`

## Get specific claim details

> Response example

```json
{
  "user_id": 101,
  "pet_id": 2345,
  "place_id": 27015,
  "pet_insurance_id": 66,
  "visit_type": "wellness exam",
  "symptoms_start_date": "2018-11-01",
  "symptoms": [ "Vomiting", "Diarrhea", "Loss of appetite" ],
  "diagnosis": "Gastroenteritis",
  "is_new_condition": true,
  "was_claimed_before": false,
  "should_create_request": true,
  "signature": "https://s3.aws.amazon.com/pawprint-claims/sig.png",
  "status": "submitted",
  "invoice_number": "6425537",
  "invoice_date": "2018-11-10",
  "invoice_total": 456.78,
  "files": [
    "https://s3.aws.amazon.com/pawprint-files/urinalysis-2345-105617.pdf",
    "https://s3.aws.amazon.com/pawprint-files/invoice-1345-023489.pdf"
  ]
}
```

Gets a specific claim.

### HTTP Request
`GET /user/claims/1345`