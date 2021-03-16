# Payments V2
This is the interface for client payments.

## Get invoice by ID

> Response example

```json
{
  "amount": 10,
  "description": "Dental cleaning on 2/16/2021"
}
```

Gets an invoice. *** Only amount is for sure; description and possibly line items is TBD pending design review ***.

### HTTP Request
`GET /invoice/:invoice_external_id`

## List payment instruments by invoice ID

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

For invoices associated with an intake, the intake's client is used (invoice.intake.user.id) as the identity for the wallet owner.
In case the intake's client has payment instruments registered with multiple practices,
this returns only the payment instruments associated with the practice that created the given invoice.

### HTTP Request
`GET /invoice/:invoice_external_id/payment_instrument`

## Remove payment instrument by invoice ID

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
  }
]
```

Removes a saved card.

For invoices associated with an intake, the intake's client is used (invoice.intake.user.id) as the identity for the wallet owner.
In case the intake's client has payment instruments registered with multiple practices,
this returns only the remaining payment instruments associated with the practice that created the given invoice.

### HTTP Request
`DELETE /invoice/:invoice_external_id/payment_instrument/:payment_instrument_id`


## Charge invoice with previously saved payment instrument

> Request example

```json
(none)
```

> Response example

```json
{
  "amount": 10,
  "amountProcessed": 9.90,
  "amountBalance": 0.10
}
```

Charges the given payment instrument and returns the amount processed and remaining balance after processing (in case of partial payments).
This only works if the payment instrument was previously saved; for one-time payments where card is not saved, use *** a different flow (TBD) ***

### HTTP Request
`POST /invoice/:invoice_external_id/payment_instrument/:payment_instrument_id`

## Initiate payment with new payment instrument

> Response example

```json
{
  "token": "0123456789ABCDEF"
}
```

Initiates a CreditSale transaction with Gravity and returns the transaction token.

### HTTP Request
`POST /invoice/:invoice_external_id/gravity`
