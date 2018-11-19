# Requests v3
This follows the pattern of POST /request/{producttype}/preview, which returns a preview invoice,
then a follow-up POST /request/{producttype}/order with the same request object to execute the transaction.

## Get current products
> Response example

```json
[
	{
		"id": 3,
		"slug": "basic",
		"title": "Basic vaccination records",
		"description": "Basic vaccination records for 1 pet at 1 vet",
		"cost": "4.99"
	},
	{
		"id": 6,
		"slug": "full",
		"title": "Full medical records",
		"description": "Full medical records for 1 pet at 1 vet",
		"cost": "9.99"
	},
	{
		"id": 8,
		"slug": "web_basic",
		"title": "Basic vaccination records",
		"description": "Basic vaccination records for 1 pet at 1 vet (submitted via web)",
		"cost": "4.99"
	},
	{
		"id": 9,
		"slug": "web_full",
		"title": "Full vaccination records",
		"description": "Full medical records for 1 pet at 1 vet (submitted via web)",
		"cost": "9.99"
	}
]
```

Gets current products in the product catalog. Each slug can have multiple product IDs associated with it,
but only 1 product per slug will be returned. This allows us to change pricing or add new SKUs in the back-end
without having to reconfigure the front-end with new product IDs.

Current slugs are `basic`, `full`, `web_basic`, `web_full`.

### HTTP Request
`GET /v3/products`

## Preview order
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
	"slug": "full",
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
			"id": 6,
			"description": "Request for full medical records for Bowser from Animal Kind Veterinary Hospital",
			"cost": 9.99
		}
	],
	"subtotal": 29.97,
	"tax": 3.00,
	"discount": -5
	"total": 27.97
}
```

Returns an itemized order preview, with promo codes applied (if any).

### HTTP Request
`POST /user/v3/request/preview`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
vets | object | Keys are IDs from the `place` table; values are objects as described below
vets\[place_id\].pets | array of ints | Array of IDs from the `pet` table.
promocode | string? | If specified, attempts to apply the promo code to the order. If the promo code didn't work, HTTP 400 is returned along with an error message.

## Execute order
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
	"slug": "full",
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
	18618,
	18619
]
```

Creates the requests, which will show up in the admin portal as 'new'. In v3, `stripe_token` is required, and
we won't be creating or charging Stripe customer accounts. If a promocode is supplied but the total comes out to $0.00,
then `stripe_token` is neither required nor charged.
Returns an array of the newly created request IDs.

### HTTP Request
`POST /user/v3/request/order`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
slug | string | Name of the product type, e.g. `basic`, `full, `web_basic`, etc. See v3 Products call for the full list of slugs.
vets | object | Keys are IDs from the `place` table; values are objects as described below
vets\[place_id\].pets | array of ints | Array of IDs from the `pet` table.
vets\[place_id\].note | string | Note to this particular vet.
signature | string | URL to the image file containing the client's signature.
checkout_notes | string? | Checkout note to Pawprint.
promocode | string? | If specified, attempts to apply the promo code to the order. If the promo code didn't work, HTTP 400 is returned along with an error message.
stripe_token | string | Stripe token that will be charged for payment (one-time use)
