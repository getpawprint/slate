# Payments V2
WIP Client payments

## List payment instruments by bundle ID

> Response example

```json
{
  "cards": [
    {
      "payment_instrument_id": 10,
      "brand": "visa",
      "exp_month": 8,
      "exp_year": 2021,
      "last4": "4242"
    },
    {
      "payment_instrument_id": 12,
      "brand": "american_express",
      "exp_month": 4,
      "exp_year": 2023,
      "last4": "4242"
    }
  ]
}
```

The bundle's (intake's) client is used (intake.user.id) as the identity for the wallet owner.
In case the intake's client has payment instruments registered with multiple practices,
this returns only the payment instruments associated with the practice that created the given intake bundle.

### HTTP Request
`GET /bundle/:bundle_id/payment_instrument`

## Remove payment instrument by bundle ID

> Response example

```json
{
  "cards": [
    {
      "payment_instrument_id": 10,
      "brand": "visa",
      "exp_month": 8,
      "exp_year": 2021,
      "last4": "4242"
    }
  ]
}
```

Removes a saved card.

The bundle's (intake's) client is used (intake.user.id) as the identity for the wallet owner.
In case the intake's client has payment instruments registered with multiple practices,
this returns only the remaining payment instruments associated with the practice that created the given intake bundle.

### HTTP Request
`DELETE /bundle/:bundle_id/payment_instrument/:payment_instrument_id`
