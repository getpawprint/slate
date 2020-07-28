# Payments
We use [Stripe](http://stripe.com/) for managing payments.

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
`POST /user/:user_id/setup_intent`

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
`GET /user/:user_id/payment_methods`

## Charge a user

> Request example

```json
{
	"amount": "3000",
	"stripe_payment_method_id": "pm_jLp1XcLD"
}
```

> Response example

```json
(none)
```

Creates a Stripe PaymentIntent on the server side, then returns the Stripe `client_secret`.
The `client_secret` should be consumed on the client side by calling stripe.confirmCardPayment().

### HTTP Request
`POST /user/:user_id/payment_intent`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
amount | integer | Charge amount, in cents
stripe_payment_method_id | string | Stripe Payment Method ID, chosen from `GET /user/:user_id/payment_methods`

## List payment history

> Response example

```json
[
	TBD
]
```

Gets the user's payment history.

### HTTP Request
`GET /user/:user_id/payments`
