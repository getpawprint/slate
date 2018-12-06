# Web requests - Onboarded vets
For making web requests for vets that have onboarded with Pawprint. Their accounts
should be linked, so we don't need to submit a request; we only need to verify the user
and then email the records.

## Send records & recruiting email
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Sends an email to the vet user, with 1 of 2 possible payloads.

**When the vet_user is not linked to a Pawprint user:**
- Vaccination records from the `vet_pet_history` table and items from the `vet_invoice` table
- CTA message to download the Pawprint app
- Branch link with the following:
  - `context`: `web_request_signup`
  - `email`: email address to prepopulate in the account creation page in the app
  - `vet_user_id`: `vet_user.id` value

**When the vet_user is linked to a Pawprint user:**
- Same email as going into the app and selecting "Share medical records" on the home screen

### HTTP Request
`POST /vet_user/:vet_user_id/send_records`

## Find vet integrations for email/phone
See "Find vet integrations for email/phone" in Activation V2.

## Create vet_user PIN
> Request example

```json
(none)
```

> Response example

```json
(none)
```

Creates a PIN for the vet_user account, valid for 1 hour, and then sends it via SMS to the vet_user's
phone number on file.

### HTTP Request
`POST /vet_user/:vet_user_id/pin`

## Verify PIN and send pet records
> Request example

```json
{
	"pin": "012345",
	"email": "demo@getpawprint.com"
}
```

> Response example

```json
{
	"status": "email_sent",
	"email": "demo@getpawprint.com"
}

{
"status": "invalid_pin",
"email": null
}

{
"status": "no_email",
"email": null
}
```

Submits a PIN for verification. If the PIN matches the PIN on the given vet_user account and isn't expired,
pet health records are sent to the given email.
If the `email` parameter was not specified, the `vet_user` is checked for an email address, and records are sent to that email.

If the PIN doesn't match or is expired, HTTP 401 is returned with `status: invalid`.

<aside class="notice">
We don't have writeback access to vet PMSes to update the email address. However, the records email
will contain a Branch link encouraging the user to sign up, and they can sign up for a Pawprint account
with any email they want.
</aside>

### HTTP Request
`PUT /vet_user/:vet_user_id/pin`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
pin | string | PIN that was generated
email | string? | Email address to send records to.

## Send records for Wag user
> Request example

```json
{
  "slug": "wag_full",
  "signature": "https://s3.aws.amazon.com/pawprint/signature.png"
}
```

> Response example

```json
(none)
```

Possibilities:

***`slug` is `wag_basic`:***

Sends an email containing records of each active vet_pet associated with the vet_user account
and logs an item for each pet in the `wag_request` table.

***`slug` is `wag_full`:***

A consent signature URL is required in the `signature` parameter in this case.

- _vet_user is linked to a Pawprint account:_ Creates a records request for each linked pet.
- _vet_user is not linked to a Pawprint account but matches the email on one:_
Returns an error message telling the user to contact us to resolve the situation manually
- _vet_user is not linked to a Pawprint account and no email match:_
Creates a Pawprint account from the vet_user account and onboards all their active pets,
then creates a records request for each pet that was onboarded.

Each request created is logged into the `wag_request` table.

### HTTP Request
`POST /vet_user/:vet_user_id/send_records/wag`
