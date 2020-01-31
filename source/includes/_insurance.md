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


## Create a claim

> Request example

```json
{
  "pet_id": 2345,
  "place_id": 27015,
  "pet_insurance_id": 66,	
  "visit_type": "wellness exam",	
  "symptoms_start_date": "2018-11-01",
  "symptoms": [ "Diarrhea", "Vomiting", "Lack of appetite" ],
  "diagnosis": "Gastroenteritis",	
  "is_new_condition": true,	
  "was_claimed_before": false,	
  "should_create_request": true,	
  "treatment_start_date": "2018-11-01",
  "treatment_end_date": "2018-11-08",
  "signature": "https://s3.aws.amazon.com/pawprint-claims/sig.png",	
  "invoice_number": "6425537",
  "invoice_date": "2018-11-10",
  "invoice_total": 253.87,
  "files": [ 167, 168 ],
  "additional_insurance": {
    "name": "ASPCA",
    "cancel_date": "2018-11-18"
  }
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
pet_id | int| ID from the `pet` table. The user must be the pet's owner (`pet.owner`)...?
place_id | int | The vet where invoices are coming from for this claim.
pet_insurance_id | int | ID from the `pet_insurance` table.
visit_type | string | One of "wellness", "injury", "accident", or... Freeform string?
symptoms_start_date | datetime or string | If a datetime is passed, only the date will be stored and the time portion will be truncated. Stored as a timezone-agnostic string in the database.
symptoms | string[] | List of symptoms
diagnosis | string | Freeform string of the vet's diagnosis
is_new_condition | bool | Answer to "This is a new condition"
was_claimed_before | bool | Answer to "I have filed a claim for this condition previously"
treatment_start_date | datetime? or string? | If a datetime is passed, only the date will be stored and the time portion will be truncated. Stored as a timezone-agnostic string in the database.
treatment_end_date | datetime? or string? | If a datetime is passed, only the date will be stored and the time portion will be truncated. Stored as a timezone-agnostic string in the database. This field is optional and a null value represents an ongoing treatment.
should_create_request | bool | Answer to "Request records from my vet for this visit"; will not submit claim until request is complete
signature | string | URL to signature file
invoice_number | string | invoice number; pick one if the user submitted multiple
invoice_date | datetime or string | invoice date; pick one if the user submitted multiple
invoice_total | number | Sum of all invoice amounts
files | int[] | User-uploaded files from the `file` table. All the pet's `pdfRecords` (official vet records) will be automatically attached to the insurance claim.
additional_insurance | object? | If the user has additional assurance; `null` means no.
additional_insurance.name | string | The other insurance company's name
additional_insurance.cancel_date | date? | Cancellation date of the other policy; `null` means the other policy is still active.


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
    "status": "complete",
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
  "treatment_start_date": "2018-11-01",
  "treatment_end_date": "2018-11-08",
  "should_create_request": true,
  "signature": "https://s3.aws.amazon.com/pawprint-claims/sig.png",
  "status": "complete",
  "invoice_number": "6425537",
  "invoice_date": "2018-11-10",
  "invoice_total": 456.78,
  "files": [
    {
      "link": "https://pawprint-file-upload.s3-us-west-2.amazonaws.com/84080-37683-1536632601600.jpg",
      "name": "Glumpy's invoice image"
    },
    {
      "link": "https://pawprint-file-upload.s3-us-west-2.amazonaws.com/104048-37683-1553621977158.pdf",
      "name": "My PDF document"
    }
  ],
  "requests": [
    {
      "id": 117,
      "status": "complete",
      "place_name": "Pawprint Demo Vet"
    }
  ]
}
```

Gets a specific claim.

### HTTP Request
`GET /user/claims/1345`

## Record request preview from a claim
> Request example

```json
{
  "place_ids": [ 27015, 1],
	"promocode": "mars"
}
```

> Response example

```json
{
	"items": [
		{
			"id": 6,
			"description": "Request for full medical records for Bella from Pawprint, Inc.",
			"cost": 9.99
		},
    {
			"id": 6,
			"description": "Request for full medical records for Bella from Pawprint, Inc.",
			"cost": 9.99
		}
	],
	"subtotal": 19.98,
	"discount": -5
	"total": 14.98
}
```

Generates an order preview for creating a record request from an insurance claim.
The pet and vet information is known from the claim, and the product ID is automatically
selected based on the insurance company in the claim.

### HTTP Request
`GET /user/claims/1345/preview`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
place_ids | int[] | Vets for making record requests
promocode | string? | If specified, attempts to apply the promo code to the order. If the promo code didn't work, HTTP 400 is returned along with an error message.

## Record request order from a claim
> Request example

```json
{
  "place_ids": [ 27015, 1],
	"signature": "https://s3.aws.amazon.com/pawprint/sig.png",
	"checkout_notes": "Checkout note to Pawprint",
	"promocode": "mars",
	"stripe_token": "tk_421"
}
```

> Response example

```json
[
	18617,
  18618
]
```

Executes an order.
Creates the requests, which will show up in the admin portal as 'new'. `stripe_token` is mostly required, and
we won't be creating or charging Stripe Customer accounts. If a promocode is supplied but the total comes out to $0.00,
or if the product is free, then `stripe_token` is neither required nor charged.
Returns an array of the newly created request IDs.

### HTTP Request
`GET /user/claims/1345/order`

Parameter | Type | Description
--------- | ---- | -----------
place_ids | int[] | Vets for making record requests
signature | string | URL to the image file containing the client's signature.
checkout_notes | string? | Checkout note to Pawprint.
promocode | string? | If specified, attempts to apply the promo code to the order. If the promo code didn't work, HTTP 400 is returned along with an error message.
stripe_token | string? | Stripe token that will be charged for payment (one-time use)

## Request insurance quote
> Request example

```json
{
  "user": {
    "first_name": "Eric",
    "last_name": "Choi",
    "zip": "98004",
    "email": "echoi@getpawprint.com"
  },
  "pets": [
    {
      "name": "Billy",
      "species": "dog",
      "breeds": [ "Labrador Retriever" ],
      "birthdate": "2019-02-20",
      "gender": "m",
      "neuter": true
    }
  ],
  "companies": [ "6" ]
}
```

> Response example

```json
[
  {
    "insurance_id": 6,
    "link": "http://healthypawspetinsurance.com/quote/retrievequote/?sessionid=echoi@getpawprint.com"
  }
]
```

Public API. Requests an insurance quote from one or more companies and returns a URL where the quote can be viewed.
If the email address in the `user` object matches an existing user's, the link will be saved to the `pet_insurance_quote` table.
Other user profile information will not be updated because this is a public API.

Quotes from multiple companies can be requested; the only companies currently supported are "6" (Healthy Paws) and "5" (Embrace).

### HTTP Request
`POST /insurance_quotes`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
user | object | Client profile.
user.first_name | string | Client's first name.
user.last_name | string | Client's first name.
user.zip | string | Client's first name.
pets | object[] | Pets for insuring.
pets.name | string | Pet's name.
pets.species | string | `dog` or `cat`.
pets.breeds | string[] | List of 0 or more breed names from the `breed` table.
pets.birthdate | date | Pet's birthdate.
pets.gender | string | `m` or `f`.
pets.neuter | boolean? | Pet's neuter status.
companies | string[] | Insurance company IDs from which a quote is requested.

## Get user's insurance quotes

> Response example

```json
[
    {
        "updated_at": "2020-01-28T22:40:52.292Z",
        "link": "http://healthypawspetinsurance.com/quote/retrievequote/?sessionid=echoi@getpawprint.com",
        "insurance_id": 6,
        "name": "Healthy Paws",
        "logo": "healthypaws.png",
        "description": null
    }
]
```

Gets all insurance quotes that were requested by the user.

### HTTP Request
`GET /user/insurance_quotes`