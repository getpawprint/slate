# Vetstoria
Vetstoria integration. After booking an appointment in Vetstoria, their clients are redirected to a URL on the Pawprint site.
Each partner hospital has a redirect URL configured on the Vetstoria side, and has a site hash that is stored in the Pawprint database.
Site hashes are obtained from our partner account on Vetstoria.

## Get Vetstoria booking details

> Request example
(none)

> Response example

```json
{
  "banner_image": "https://s3.aws.amazon.com/pawprint/pawprint-images/partner_logo.jpg",
  "user": {
    "first_name": "Jane",
    "last_name": "Smith",
    "phone": "(555) 555-5555",
    "email": "janesmith@getpawprint.com"
  },
  "bookings": [
    {
      "external_id": "a4X_ilj39-cA41nb0c9rip9",
      "pet": {
        "name": "Milo",
        "species": "dog"
      },
      "appointment": {
        "date": "2019-07-20",
        "time": "9:15 am",
        "timezone": "America/Los_Angeles",
        "type": "Wellness Exam",
        "reason": "Just moved here from Chicago"
      }
    },
    {
      "external_id": "m-49u4uVnHLk56rm47nu",
      "pet": {
        "name": "Pumpkin",
        "species": "cat"
      },
      "appointment": {
        "date": "2019-07-20",
        "time": "9:30 am",
        "timezone": "America/Los_Angeles",
        "type": "Consultation",
        "reason": "Just moved here from Chicago"
      }
    },
  ],
}
```
Gets appointment information from Vetstoria and returns it to the caller (Pawprint landing page), and also silently creates intakes
in the Pawprint database. A Vetstoria booking ID may return multiple appointments. The `place_id` parameter will be hard coded into
the redirect URL that we give to Vetstoria per partner hospital, and `booking_id` is an identifier generated on their end.
The result is that both parameters will end up in the URL when they redirect to Pawprint.

### HTTP Request
`POST /partners/vetstoria/:place_id/:booking_id`

