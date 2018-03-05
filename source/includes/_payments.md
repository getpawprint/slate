# Payments
We use [Stripe](http://stripe.com/) for managing payments.

## Update payment information
> Request example

```json
{
	"token": "tok_b3ad9ff2c01vcf"
}
```

> Response example

```json
(none)
```

Sets the user's payment source with the given Stripe token. If a Stripe customer doesn't exist yet,
the token is used to create a Stripe customer with a payment source. If the Stripe customer does exist,
the token is used to update the Stripe customer's payment source.

<aside class="notice">
Stripe tokens can be used only once, and then they are considered consumed.
It is generated from payment information by the client, and can be used to
either set up an account with a payment source, or update the payment source on
an existing account.
</aside>

### HTTP Request
`POST /user/stripe/:userid/source`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
token | string | Stripe token returned by the client-side Stripe API.
