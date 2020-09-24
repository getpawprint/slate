# Vet Billing
This is for the vets' subscriptions for services that we provide; see the Payments section for client payments.

## Create a Setup Intent

> Response example

```json
{
	"client_secret": "jLp1XcLD"
}
```

For setting up a payment method.

Creates a Stripe SetupIntent on the server side, then returns the Stripe `client_secret`.
The `client_secret` should be consumed on the client side by calling `stripe.confirmCardSetup()` (https://stripe.com/docs/js/setup_intents/confirm_card_setup#stripe_confirm_card_setup-with_element).

### HTTP Request
`POST /partners/billing/setup_intent`

## List payment methods

> Response example

```json
[
	{
		"stripe_payment_method_id": "pm_jLp1XcLD",
		"brand": "visa",
		"exp_month": 8,
		"exp_year": 2021,
		"last4": "4242"
	}
]
```

Gets all the payment methods for a user, which can later be used to create a charge.
Note that `exp_month` is 1-based, i.e. January = 1 and December = 12, instead of Javascript's 0-based months.


### HTTP Request
`GET /partners/billing/payment_methods`

## List payment history

> Response example

```json
[
	{		
		"date": "2020-05-20",
		"description": "May intake subscription",
		"amount": "20000",
		"status": "complete",
		"stripe_payment_id": "pi_1H9zrzDZqIsjxz1b7Iw5VLmL"
	}
]
```

Gets the user's payment history. `amount` is in cents. `status` is based on https://stripe.com/docs/payments/intents#intent-statuses and is one of the following:

- `requires payment method`
- `requires confirmation`
- `requires action`
- `pending`
- `cancelled`
- `complete`

### HTTP Request
`GET /partners/billing/payments`
