# Payments V2
Terminology:

* **Payment method** - A class of payment instruments: Card, check, cash, loan, money order, etc.
* **Payment instrument** - A specific instance of a payment method, e.g. credit card ending with 4242, loan ID etc. Practically, only applicable to card and loan payment types.
* **Charge** - An amount owed by someone to the practice. A charge can be associated with one or more intakes (appointments).
* **Payment** - A transfer of money (using a payment instrument) to pay off the balance on a charge. A charge can be paid off with multiple payments.
* **Invoice** - A detailed description (possibly including line items) of the charge.

All money amounts are in cents because Javascript Numbers are imprecise floating point values,
for example, try running `node -e "console.log(0.1 + 0.2)"` from a console.

## Create charge

> Request example

```json
{
  "amount": 2345,
  "notes": "Pumpkin and Roger exam + vax",
  "user_id": 101,
  "vet_user_id": null,
  "intake_ids": [42, 43],
  "email": "johnsmith@snoutid.com",
  "phone": "+15555551234",
  "files": [{
    "description": "Invoice for PUMPKIN EXAM",
    "data": "e0b7YUamJuV5Ln34zpjaes236w3RJi0j"
  }]
}
```

> Response example

```json
{
  "external_id": "gH_zis2b",
  "amount": 12345,
  "status": "pending",
  "created_at": "2021-03-21T12:30:21-04:00:00",
  "completed_at": null,
  "payments": [
  ],
  "place": {
    "id": 3,
    "name": "Super Test Veterinary Clinic",
    "address": "123 Main St, Bellevue, WA 98004",
    "phone": "(555) 555-4567",
    "banner_image": "https://api.scoutvet.com/images/logo-header.png"
  },
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "payment_instruments": {
      "cards": [
        {
          "payment_instrument_id": 10,
          "brand": "visa",
          "exp_month": 8,
          "exp_year": 2021,
          "last4": "4242",
          "default": true
        }
      ]
    }
  },
  "intakes": [
    {
      "pet": {
        "name": "Mochi",
        "species": "dog", 
        "breed": "Maltese",
        "gender": "m",
        "profile_pic": "https://pawprint-user-upload.s3-us-west-2.amazonaws.com/68795-37683-1502212252631.jpg"
      },
      "appointment": {
        "type": "Canine Neuter",
        "date": "2021-04-02",
        "time": "4:30 PM"
      }
    }
  ],
  "files": [{
    "description": "Invoice for PUMPKIN EXAM",
    "url": "https://snout-vet-invoice.s3-us-west-2.amazonaws.com/2394872834.pdf"
  }]
}
```

Creates a charge and optionally emails or SMSes it to the client.

`intake_ids`, `user_id` and `vet_user_id` are all optional.

- If `intake_ids` is used, then those intakes will be associated with this charge, which means the recipient will have access to those appointments' details and they can use a saved payment instrument.
- If `user_id` is used, then the charge will be associated with this user, which means they can use a saved payment instrument.
- If `vet_user_id` is used, then a Snout user account will be created using data from the vet's PMS, and the charge will be associated with the new user account's ID.
`vet_user_id` is obtained from the client listing/search API - sometimes the vet may select a client who hasn't interacted with Snout yet.
- If none of `user_id`, `vet_user_id` or `intake_ids` is used, then an anonymous charge will be created.

Specifying `email` and/or `phone` will cause a remote payment link (and reminders, if any) to be sent to their respective destinations; they can be different from the user's email/phone on file (linked through `user_id` or `intake_ids`).

Errors:

- HTTP 400/Bad Request if the payment amount is 0, negative or not a Javascript safe integer.
- HTTP 400/Bad Request if the `user_id` doesn't exist.
- HTTP 400/Bad Request if any of the intake IDs don't exist, or intakes belong to different clients.
- HTTP 400/Bad Request if `email` is obviously not an email address.
- HTTP 400/Bad Request if `phone` is obviously not a phone number.
- HTTP 400/Bad Request if an uploaded file is not in an acceptable format.

### HTTP Request
`POST /partners/charge`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
amount | int | Payment amount, in cents
notes| string? | Freeform text field for practice's use
intake_ids | int[]? | Intake IDs to associate with this charge
user_id | int? | User ID to associate with this charge; ignored if intakes were passed
vet_user_id | int? | Creates (or reuses) a user account from this PMS account; ignored if user_id or intake_ids were passed
email | string? | Specifying this will cause a remote payment link, receipt and any reminders to be emailed to this address
phone | string? | Specifying this will cause a remote payment link, receipt and any reminders to be SMSed to this phone number. It should be in E.164 format (e.g. +14155552671).
files | object[]? | List of invoice files to upload with this charge
files.description | string? | Description of the file; defaults to "Invoice file uploaded on _date_"
files.data | string | base64 string of the data. JPG, PNG or PDF only.

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

## Remove payment instrument by charge ID

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

`payment_instrument_id` and `charge_external_id` are both required to verify that the charge's user
and the payment instrument's owner are the same.
In case the intake's client has payment instruments registered with multiple practices,
this returns only the remaining payment instruments associated with the practice that created the charge.
Payment instruments are soft-deleted so that existing payments can still reference them.

### HTTP Request
`DELETE /charge/:charge_external_id/payment_instrument/:payment_instrument_id`

## Get a charge by charge ID

> Response example

```json
{
  "external_id": "gH_zis2b",
  "amount": 12345,
  "status": "complete",
  "email": "js+personal@snoutid.com",
  "phone": "+14155556789",
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
  ],
  "place": {
    "id": 3,
    "name": "Super Test Veterinary Clinic",
    "address": "123 Main St, Bellevue, WA 98004",
    "phone": "(555) 555-4567",
    "banner_image": "https://api.scoutvet.com/images/logo-header.png"
  },
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@snoutid.com",
    "phone": "+14155552671",
    "payment_instruments": {
      "cards": [
        {
          "payment_instrument_id": 10,
          "brand": "visa",
          "exp_month": 8,
          "exp_year": 2021,
          "last4": "4242",
          "default": true
        }
      ]
    }
  },
  "intakes": [
    {
      "pet": {
        "name": "Mochi",
        "species": "dog", 
        "breed": "Maltese",
        "gender": "m",
        "profile_pic": "https://pawprint-user-upload.s3-us-west-2.amazonaws.com/68795-37683-1502212252631.jpg"
      },
      "appointment": {
        "type": "Canine Neuter",
        "date": "2021-04-02",
        "time": "4:30 PM"
      }
    }
  ],
  "files": [{
    "description": "Invoice for Mochi's Canine Neuter",
    "url": "https://snout-vet-invoice.s3-us-west-2.amazonaws.com/2394872834.pdf"
  }]
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

## Make a payment on a charge (from client portal)

> Request example

```json
{
  "payment_instrument_id": 10,
  "amount": 2345,
  "notes": "Initial deposit"
}
```

> Response example

```json
{
  "amount": 10000,
  "method": "card",
  "notes": "Initial deposit",
  "status": "complete",
  "message": null
}
```

Amount is in cents, and may be less than the remaining balance on the charge.
Returns the newly created payment object.
If the payment instrument could not be charged (e.g. insufficient balance or credit limit exceeded),
the payment's `status` will be `failed` and the error message will be in the `message` field.

Errors:

- HTTP 400/Bad Request if the payment amount exceeds the remaining balance on the charge, or the amount is 0, negative or not a Javascript safe integer.
- HTTP 404/Resource Not Found if the payment instrument does not exist (including the case where the payment instrument for the client was saved from a different practice)

### HTTP Request
`POST /charge/:charge_external_id/payment`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
payment_instrument_id | integer | ID of a previously saved payment instrument
amount | int | Payment amount, in cents
notes| string? | Freeform text field for practice's use

## Make a payment on a charge (from vet portal)

> Request example

```json
{
  "payment_instrument_id": 10,
  "amount": 2345,
  "notes": null
}

// Cash form
{
  "method": "cash",
  "amount": 100000,
  "notes": "Initial deposit"
}

// Check form
{
  "method": "check",
  "amount": 100000,
  "notes": "Initial deposit",
  "drivers_license_number": "EC131K*WA",
  "drivers_license_state": "WA"
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
If the payment instrument could not be charged (e.g. insufficient balance or credit limit exceeded),
the payment's `status` will be `failed` and the error message will be in the `message` field.

Errors:

- HTTP 400/Bad Request if the payment amount exceeds the remaining balance on the charge, or the amount is 0, negative or not a Javascript safe integer.
- HTTP 404/Resource Not Found if the payment instrument does not exist (including the case where the payment instrument for the client was saved from a different practice)

### HTTP Request
`POST /partners/charge/:charge_external_id/payment`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
payment_instrument_id | integer? | ID of a previously saved payment instrument; 
method | string? | Charge description; required if payment_instrument_id is omitted. Valid values are `cash` or `check`.
amount | int | Payment amount, in cents
notes| string? | Freeform text field for practice's use
drivers_license_number | string? | Driver's license number (only considered for `check` method)
drivers_license_state | string? | Driver's license state (only considered for `check` method)

## Send payment receipt

> Request example

```json
{
  "email": "johnsmith@snoutid.com",
  "phone": "+15555551234"
}
```

> Response example

```json
(none)
```

Re-sends a payment receipt to the specified email or phone.

Errors:

- HTTP 400/Bad Request if `email` is obviously not an email address.
- HTTP 400/Bad Request if `phone` is obviously not a phone number.

### HTTP Request
`POST /partners/charge/:charge_external_id/receipt`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string? | Specifying this will cause the receipt to be emailed to this address
phone | string? | Specifying this will cause the receipt to be SMSed to this phone number. It should be in E.164 format (e.g. +14155552671).