# Gravity Payments
Calls specific to the Gravity Payments flow.

## Get a CreditSaveCard transaction token

> Request example

```json
(none)
```

> Response example

```json
{
	"token": "0123456789abcdef"
}
```

Gets a transaction token with type CreditSaveCard from Gravity; the intent is to save a card for later charges.
`bundle_id` is used to identify the client to whom the card belongs; it is a private, opaque identifier for a group of intakes belonging to the same client.

### HTTP Request
`POST /intake/bundle/:bundle_id/gravity_card`

## Start CreditSale transaction

> Request example

```json
{
  "save_card": true,
  "device_name": "my_credit_card_reader",
  "amount": 100
}
```

> Response example

```json
{
	"token": "0123456789abcdef"
}
```

Gets a transaction token with type CreditSale from Gravity. If `save_card` is true, and the charge is linked to a completed intake or user account,
the card is saved to the user account for later use after the payment succeeds.

### HTTP Request
`POST /charge/:charge_external_id/gravity_sale`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
save_card | boolean? | If the charge is linked to a completed intake or user account, the card is saved to the user account for later use after the payment succeeds.
device_name | string? | Used to identify a Gravity hardware payment device for card present payments.
amount | integer? | Amount in cents. Optional - only for partial payments; otherwise the amount is assumed to be the remaining amount from the charge.

## Gravity webhook

> Request example

 ```json
 {
  "uniqueTransId": "77c87064222c4bf88c27f0865d176cf0-0e7453dc741e4233ad571d6ca8d73587",
  "batchNumber": "115",
  "resultMessage": "Approved",
  "resultStatus": "true",
  "approvalNumberResult": "039275",
  "amountProcessed": "0.01",
  "amount": "0.01",
  "amountBalance": "",
  "transactionReference": "",
  "avsResponseCode": "Y",
  "avsResponseText": "Address: Match & 5 Digit Zip: Match",
  "cvvResponseCode": "P",
  "cvvResponseText": "Not Processed",
  "accountCardType": "AX",
  "accountExpiryDate": "0123",
  "transactionType": "CreditSale",
  "billingName": "",
  "maskedAccount": "***********1111",
  "accountEntryMethod": "Keyed",
  "externalTransactionId": "cad8d603-0c51-478a-b410-c5ffa7425be1"
}
 ```

> Response example

```json
(none)
```

Called by Gravity after a CreditSaveCard or CreditSale transaction has run.

### HTTP Request
`POST /gravity/card`