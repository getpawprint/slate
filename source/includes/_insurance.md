# Insurance claims
API to create an insurance claim. Each claim consists of a user, pet, place, claim-specific information, and one or more invoice documents. The user may create a record request while filling out the claim; the claim will then only be submitted after the record request is complete. If the user opts out of the record request, then the claim will be submitted as soon as the user is finished filling it out.

## Create a claim

> Request example

```json
{
  "user_id": 101,
  "pet_id": 2345,
  "place_id": 27015,
  "pet_insurance_id": 66
}
```

> Response example

```json
{
  "id": 1345
}
```

Creates a claim, which uses the information to create a skeleton row in the database and reserves an ID.

### HTTP Request
`POST /user/claim`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
user_id | int | ID from the `user` table.
pet_id | int| ID from the `pet` table. The user must be the pet's owner (`pet.owner`)...?
place_id | int | The vet where invoices are coming from for this claim.
pet_insurance_id | int | ID from the `pet_insurance` table.

## Update claim

> Request example

```json
{
  "place_id": 27015,
  "pet_insurance_id": 66,
  "visit_type": "wellness exam",
  "symptoms_start_date": "2018-11-01",
  "diagnosis": "Diabetes Mellitus",
  "is_new_claim": true,
  "was_claimed_before": false,
  "should_create_request": true,
  "signature": "https://s3.aws.amazon.com/pawprint-claims/sig.png",
  "invoice": {
    "number": "6425537",
    "date": "2018-11-10",
    "total": 253.87
  },
  "files": [
    "https://s3.aws.amazon.com/pawprint-files/urinalysis-2345-105617.pdf",
    "https://s3.aws.amazon.com/pawprint-files/invoice-1345-023489.pdf"
  ]
}
```

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
  "diagnosis": "Diabetes Mellitus",
  "is_new_claim": true,
  "was_claimed_before": false,
  "should_create_request": true,
  "signature": "https://s3.aws.amazon.com/pawprint-claims/sig.png",
  "invoices": [
    {
      "number": "6425537",
      "date": "2018-11-10",
      "total": 253.87
    },
    {
      "number": "6425540",
      "date": "2018-11-12",
      "total": 160.00
    }  
  ],
  "files": [
    "https://s3.aws.amazon.com/pawprint-files/urinalysis-2345-105617.pdf",
    "https://s3.aws.amazon.com/pawprint-files/invoice-1345-023489.pdf"
  ]
}
```

Searches Google Places Autocomplete for cities for a given prefix, e.g. `prefix=Aberdeen`. Geocoordinates are not returned
because Google Places Autocomplete doesn't return them; when performing a location aware search, pass in the `placesid` instead
and the backend will get its coordinates automatically.

### HTTP Request
`PUT /user/claim/1345`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
place_id | int? | The vet where invoices are coming from for this claim.
pet_insurance_id | int? | ID from the `pet_insurance` table.
visit_type | string? | One of "wellness", "injury", "accident", or... Freeform string?
symptoms_start_date | datetime? or string? | If a datetime is passed, only the date will be stored and the time portion will be truncated. Stored as a timezone-agnostic string in the database.
symptoms | string[]? | List of symptoms
diagnosis | string | Freeform string of the vet's diagnosis
is_new_condition | bool? | Answer to "This is a new condition"
was_claimed_before | bool? | Answer to "I have filed a claim for this condition previously"
should_create_request | bool? | Answer to "Request records from my vet for this visit"; will not submit claim until request is complete
signature | string? | URL to signature file
invoices | object[]? | Invoice(s) from the vet
invoice.number | string | Invoice number
invoice.date | datetime or string | If a datetime is passed, only the date will be stored and the time portion will be truncated. Stored as a timezone-agnostic string in the database.
invoice.total | number | Grand total on the invoice; claim amount
files | string[]? | Documents to attach to the insurance claim

## Get user's existing claims

> Response example

```json
[
  {
    "id": 10,
    "user_id": 101,
    "pet_id": 2345,
    "place_id": 27015,
    "insurance_name": "Pawprint Insurance Co.",
    "insurance_policy": "A000007",
    "invoice_total": 456.78,
    "status": "Waiting for records",
    "created_at": "2018-12-01",
    "updated_at": "2018-12-01"
  }
]
```

Gets a summary of the user's existing insurance claims.

### HTTP Request
`GET /user/claim`

## Get existing claim details

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
  "diagnosis": "Diabetes Mellitus",
  "is_new_claim": true,
  "was_claimed_before": false,
  "should_create_request": true,
  "signature": "https://s3.aws.amazon.com/pawprint-claims/sig.png",
  "status": "submitted",
  "invoices": [
    {
      "number": "6425537",
      "date": "2018-11-10",
      "total": 253.87
    },
    {
      "number": "6425540",
      "date": "2018-11-12",
      "total": 160.00
    }  
  ],
  "files": [
    "https://s3.aws.amazon.com/pawprint-files/urinalysis-2345-105617.pdf",
    "https://s3.aws.amazon.com/pawprint-files/invoice-1345-023489.pdf"
  ]
}
```

Gets a specific claim.

### HTTP Request
`GET /user/claim/1345`