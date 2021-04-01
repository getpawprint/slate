# Payments V2
Terminology:

* **Payment method** - A class of payment instruments: Card, check, cash, loan, money order, etc.
* **Payment instrument** - A specific instance of a payment method, e.g. credit card ending with 4242, loan ID etc. Practically, only applicable to card and loan payment types.
* **Charge** - An amount owed by someone to the practice. A charge can be associated with one or more intakes (appointments).
* **Payment** - A transfer of money (using a payment instrument) to pay off the balance on a charge. A charge can be paid off with multiple payments.
* **Invoice** - A detailed description (possibly including line items) of the charge.

All money amounts are in cents because Javascript Numbers are imprecise floating point values,
for example, try running `node -e "console.log(0.1 + 0.2)"` from a console.

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
Payment instruments are soft-deleted so that existing payments can still reference them.

### HTTP Request
`DELETE /intake/bundle/:bundle_id/payment_instrument/:payment_instrument_id`

## Get a charge by charge ID

> Response example

```json
{
  "external_id": "gH_zis2b",
  "amount": 12345,
  "status": "complete",
  "created_at": "2021-03-21T12:30:21-04:00:00",
  "completed_at": "2021-03-21T12:33:42-04:00:00",
  "payments": [
    {
      "amount": 10000,
      "method": "cash",
      "status": "complete",
      "notes": "deposit"
    },
    {
      "amount": 2345,
      "method": "card",
      "status": "complete",
      "notes": null
    }
  ]
}
```

Gets a charge. Charges are created by the vet and may be associated with a user (account balance), intake (appointment-specific) or neither (anonymous charge).
Amounts are in cents.

Charge `status` is either `pending` or `complete`. The status will be `complete` if the sum of payment amounts is greater than or equal to the charge amount,
or if the vet overrides it (write-off).

`instrument` is one of `card`, `cash` or `check`; `loan` may be added in the future.

Payment `status` is either `pending`, `complete`, `failed` or `refunded`.

### HTTP Request
`GET /charge/:charge_external_id`

## Make a payment on a charge

> Request example

```json
{
  "payment_instrument_id": 10,
  "amount": 2345,
  "notes": null
}

// Alternate form
{
  "method": "cash",
  "amount": 100000,
  "notes": "Initial deposit"
}
```

> Response example

```json
{
  "amount": 10000,
  "method": "cash",
  "notes": "Initial deposit",
  "status": "complete",
  "message": null
}
```

Amount is in cents, and may be less than the remaining balance on the charge.
Returns the updated charge object.
If the payment instrument was unable to be charged (e.g. insufficient balance or credit limit exceeded),
the payment's `status` will be `failed` and the error message will be in the `message` field.

Errors:
HTTP 400/Bad Request if the payment amount exceeds the remaining balance on the charge, or the amount is 0, negative or not a Javascript safe integer.
HTTP 404/Resource Not Found if the payment instrument does not exist (including the case where the payment instrument for the client was saved from a different practice)

### HTTP Request
`POST /charge/:charge_external_id/payment`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
payment_instrument_id | integer? | ID of a previously saved payment instrument; 
method | string? | Charge description; required if payment_instrument_id is omitted.
amount | int | Payment amount, in cents
notes| string? | Freeform text field for practice's use
