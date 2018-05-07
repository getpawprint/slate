# Requests v2
This follows the pattern of POST /request/{producttype}/preview, which returns a preview invoice,
then a follow-up POST /request/{producttype}/order with the same request object to execute the transaction.

## Get all products
> Response example

```json
[
    {
        "id": 1,
        "title": "Basic record request",
        "description": "Basic record request. Proof of vaccination for groomers and boarders.",
        "cost": "0.00"
    },
    {
        "id": 2,
        "title": "Rush request",
        "description": "Rush request; order will be processed within 48 hours",
        "cost": "4.99"
    },
    {
        "id": 3,
        "title": "Full medical records request",
        "description": "Full medical records and your order will be processed within 48 hours",
        "cost": "9.99"
    },
    {
        "id": 4,
        "title": "Additional vet",
        "description": "Additional vet for full medical records",
        "cost": "4.99"
    },
    {
        "id": 5,
        "title": "Free rush service",
        "description": "Free rush service provided to user",
        "cost": "0.00"
    },
    {
        "id": 6,
        "title": "Full medical records request",
        "description": "Full medical records for 1 pet at 1 vet",
        "cost": "9.99"
    },
    {
        "id": 7,
        "title": "Additional pet at a vet",
        "description": "Additional pet at a vet",
        "cost": "4.99"
    }
]
```

Gets all products in the product catalog.

<aside class="notice">
Product IDs will not change; new products that are added will get new IDs, so you can
hard code product IDs into the app.
</aside>


### HTTP Request
`GET /v2/products`

## Basic order - preview
> Request example

```json
{
	"selected_vets": [ 2037, 1984 ]
}
```

> Response example

```json
{
    "items": [
        {
            "id": 1,
            "description": "Basic records for Apple from Yorktown Animal Hospital: Gariboldi Rita T DVM",
            "cost": 0
        },
        {
            "id": 1,
            "description": "Basic records for Apple from Animal Kind Veterinary Hospital",
            "cost": 0
        }
    ],
    "subtotal": 0,
    "tax": 0,
    "total": 0
}
```

Returns the order preview for basic record requests (should be free).

### HTTP Request
`POST /user/v2/pets/:petid/request/basic/preview`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
selected_vets | array of ints | Array of IDs from the `place` table.

## Basic order - execute
> Request example

```json
{
	"selected_vets": [ 2037, 1984 ],
	"signature": "https://s3.aws.amazon.com/pawprint/sig.png",
	"note": ["May be under my maiden name (Wilson)", "May be under my husband's name (Russell)"]
}
```

> Response example

```json
[
    18613,
    18614
]
```

Returns an array of the newly created request IDs.

### HTTP Request
`POST /user/v2/pets/:petid/request/basic/order`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
selected_vets | array of ints | Array of IDs from the `place` table.
signature | string |  URL to the image file containing the client's signature.
note | array of strings | Array of notes to each vet, in the same order as selected_vets.

## Full order - preview
> Request example

```json
{
	"selected_vets": [ 2037, 1984 ],
}
```

> Response example

```json
{
    "items": [
        {
            "id": 3,
            "description": "Request for full medical records for Apple from Yorktown Animal Hospital: Gariboldi Rita T DVM",
            "cost": 9.99
        },
        {
            "id": 4,
            "description": "Additional request for medical records for Apple from Animal Kind Veterinary Hospital",
            "cost": 4.99
        }
    ],
    "subtotal": 14.98,
    "tax": 1.5,
    "total": 16.48
}
```

Returns the order preview for full record requests.

### HTTP Request
`POST /user/v2/pets/:petid/request/full/preview`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
selected_vets | array of ints | Array of IDs from the `place` table.

## Full order - execute
> Request example

```json
{
	"selected_vets": [ 2037, 1984 ],
	"signature": "https://s3.aws.amazon.com/pawprint/sig.png",
	"note": ["May be under my maiden name (Wilson)", "May be under my husband's name (Russell)"]
}
```

> Response example

```json
[
    18615,
    18616
]
```

Charges the customer's credit/debit card (see Payment section) and creates the record requests.

### HTTP Request
`POST /user/v2/pets/:petid/request/full/order`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
selected_vets | array of ints | Array of IDs from the `place` table.
signature | string |  URL to the image file containing the client's signature.
note | array of strings | Array of notes to each vet, in the same order as selected_vets.

## Rush upgrade - preview
> Request example

```json
{
	"request_ids": [ 17767, 17768 ]
}
```

> Response example

```json
{
    "items": [
        {
            "id": 2,
            "description": "Upgrade request for Apple from Yorktown Animal Hospital: Gariboldi Rita T DVM",
            "cost": 4.99
        },
        {
            "id": 2,
            "description": "Upgrade request for Apple from Animal Kind Veterinary Hospital",
            "cost": 4.99
        }
    ],
    "subtotal": 9.98,
    "tax": 1,
    "total": 10.98
}
```

Returns the order preview for upgrading a basic record request to a rush request.

### HTTP Request
`POST /user/v2/pets/:petid/request/rush/preview`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
request_ids | array of ints | Array of IDs from the `request` table.

## Rush upgrade - execute
> Request example

```json
{
	"request_ids": [ 17767, 17768 ]
}
```

> Response example

```json
(none)
```

Charges the customer's credit/debit card (see Payment section) and prioritizes the given requests.

### HTTP Request
`POST /user/v2/pets/:petid/request/rush/order`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
request_ids | array of ints | Array of IDs from the `request` table.

## Full order (new pricing) - preview
> Request example

```json
{
  "vets": {
    "2037": {
      "pets": [ 1 ]
    },
    "1984": {
      "pets": [ 1, 10 ]
    }
  },
  "promocode": "mars"
}
```

> Response example

```json
{
    "items": [
        {
            "id": 6,
            "description": "Request for full medical records for Apple from Yorktown Animal Hospital: Gariboldi Rita T DVM",
            "cost": 9.99
        },
        {
            "id": 6,
            "description": "Request for full medical records for Apple from Animal Kind Veterinary Hospital",
            "cost": 9.99
        },
        {
            "id": 7,
            "description": "Additional pet: Bowser",
            "cost": 4.99
        }
    ],
    "subtotal": 29.97,
    "tax": 3.00,
    "total": 32.97,
    "discount": -5
}
```

Returns the order preview for full record requests. Pricing is as follows: $10 for each vet, and $10
for each pet after the first one (regardless of how many vets). So if you have 2 places and 2 pets, the
total cost comes out to $10 * 2 vets + $10 * 1 additional pet = $30.

### HTTP Request
`POST /user/v2/request/full/preview`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vets | object | Keys are IDs from the `place` table; values are objects as described below
vets\[place_id\].pets | array of ints | Array of IDs from the `pet` table.
promocode | string | If specified, attempts to apply the promo code to the order. If the promo code didn't work, HTTP 400 is returned along with an error message.

## Full order (new pricing) - execute
> Request example

```json
{
  "vets": {
    "2037": {
      "pets": [ 1 ],
      "note": "May be under my maiden name (Wilson)"
    },
    "1984": {
      "pets": [ 1, 10 ],
      "note": "May be under my husband's name (Russell)"
    }
  },
	"signature": "https://s3.aws.amazon.com/pawprint/sig.png",
	"checkout_notes": "Checkout note to Pawprint",
	"promocode": "mars"
}
```

> Response example

```json
[
    18617,
    18618,
    18619
]
```

Charges the customer's credit/debit card (see Payment section) and creates the record requests.
Returns array of the newly created request IDs.

### HTTP Request
`POST /user/v2/request/full/order`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vets | object | Keys are IDs from the `place` table; values are objects as described below
vets\[place_id\].pets | array of ints | Array of IDs from the `pet` table.
vets\[place_id\].note | string | Note to this particular vet.
signature | string |  URL to the image file containing the client's signature.
checkout_notes | string | Checkout note to Pawprint.
promocode | string | If specified, attempts to apply the promo code to the order. If the promo code didn't work, HTTP 400 is returned along with an error message.
