# Configuration
Switch features on and off; vet-specific customizations.

## Get configuration (vet)

> Response example

```json
{
    "account": {
        "email": "demo2@snoutid.com",
        "pawprint_email": "demo2@snoutid.com"
    },
    "place": {
        "name": "Pawprint, Inc.",
        "shortname": "demo2",
        "address": "814 Mission Street, San Francisco, CA",
        "phone": "(555) 555-5555",
        "banner_image": "https://api.scoutvet.com/images/logo-header.png"
    },
    "confirmations": {
        "enabled": true
    },
    "reminders": {
        "enabled": true,
        "appointment_website": "https://snoutid.com",
        "reminder_cadence": [
          {
            "email": true,
            "sms": true,
            "days": -1
          },
          {
            "email": true,
            "sms": false,
            "days": 7
          }
        ]
    },
    "sms_messaging": {
        "enabled": false,
        "twilio_phone_number": "+18559654492",
        "auto_reply": false,
        "auto_reply_message": "false"
    },
    "intake": {
        "vanity_slug": "demo2",
        "terms_of_service": "I agree to everything",
        "intake_form_override": {
            "user_birthdate": {
                "is_visible": false,
                "is_required": false
            },
            "address": {
                "is_visible": false,
                "is_required": false
            },
            "referral": {
                "is_visible": false,
                "is_required": false
            },
            "car_make_model": {
                "is_visible": false,
                "is_required": false
            },
            "sms_opt_in": {
                "is_visible": false,
                "is_required": false
            },
            "media_opt_in": {
                "is_visible": false,
                "is_required": false
            }
        },
        "questionnaire_enabled": true,
        "checkin_instructions": "Please wear a mask when you arrive to comply with the governor's mandate."
    }
}
```

Gets a vet configuration.

### HTTP Request
`GET /partners/settings`

## Update configuration (vet)
> Request example

```json
{
    "account": {
        "email": "demo2@snoutid.com"
    },
    "place": {
        "name": "Pawprint, Inc.",
        "shortname": "demo2",
        "address": "814 Mission Street, San Francisco, CA",
        "phone": "(555) 555-5555"
    },
    "confirmations": {
        "enabled": true
    },
    "reminders": {
        "enabled": true,
        "appointment_website": "https://snoutid.com",
        "reminder_cadence": [
          {
            "email": true,
            "sms": true,
            "days": -1
          },
          {
            "email": true,
            "sms": false,
            "days": 7
          }
        ]
    },
    "sms_messaging": {
        "auto_reply": false,
        "auto_reply_message": "false"
    },
    "intake": {
        "intake_form_override": {
            "user_birthdate": {
                "is_visible": false,
                "is_required": false
            },
            "address": {
                "is_visible": false,
                "is_required": false
            },
            "referral": {
                "is_visible": false,
                "is_required": false
            },
            "car_make_model": {
                "is_visible": false,
                "is_required": false
            },
            "sms_opt_in": {
                "is_visible": false,
                "is_required": false
            },
            "media_opt_in": {
                "is_visible": false,
                "is_required": false
            }
        },
        "checkin_instructions": "Please wear a mask when you arrive to comply with the governor's mandate."
    }
}
```

> Response example

```json
(Same as get vet configuration)
```
Updates a vet configuration; one section may be specified at a time, but all its properties must be present.

### HTTP Request
`PATCH /partners/settings`

### PATCH parameters
Parameter | Type | Description
--------- | ---- | -----------
account | object? | Account information for SnoutID emails and banner image
account.email | string | Login email
place | object? | Place information for email, phone and physical contact
place.name | string | Place's official name
place.shortname | string |  Place's short name, used in SMS messages
place.address | string | Place's physical address
place.phone | string | Place's phone number
confirmations | object? | Appointment confirmation settings
confirmations.enabled | boolean | Feature on/off
reminders | object? | Reminder settings
reminders.enabled | boolean | Feature on/off
reminders.appointment_website | string? | Booking link in reminder email
reminders.reminder_cadence | object[] | Reminder schedule
reminders.reminder_cadence.email | boolean | Whether or not an email reminder should be sent (if possible)
reminders.reminder_cadence.sms | boolean | Whether or not an SMS reminder should be sent (if possible)
reminders.reminder_cadence.days | integer | Days from expiration the reminder should be sent; positive numbers are before the due date and negative numbers are after the due date.
sms_messaging | object? | 2-way text settings; vets will always get a twilio_phone number, but must contact us to enable/disable 2-way text
sms_messaging.auto_reply | boolean | Whether or not to send an automatic reply to the client's first incoming text
sms_messaging.auto_reply_message | string | The automatic reply to the client's first incoming text
intake | object? | Intake settings
intake.intake_form_override | object | Intake form customizations
intake.intake_form_override.user_birthdate | object | Birthdate customization
intake.intake_form_override.user_birthdate.is_visible | boolean | Whether or not the input for the client's birthdate should be shown
intake.intake_form_override.user_birthdate.is_required | boolean | Whether or not the input for the client's birthdate should be required
intake.intake_form_override.address | object | Client address customization
intake.intake_form_override.address.is_visible | boolean | Whether or not the input for the client's address should be shown
intake.intake_form_override.address.is_required | boolean | Whether or not the input for the client's address should be required
intake.intake_form_override.referral | object | Referral customization
intake.intake_form_override.referral.is_visible | boolean | Whether or not the input for how the client found the vet should be shown
intake.intake_form_override.referral.is_required | boolean | Whether or not the input for how the client found the vet should be required
intake.intake_form_override.car_make_model | object | Car make and model customization
intake.intake_form_override.car_make_model.is_visible | boolean | Whether or not the input for the client's car make and model should be shown
intake.intake_form_override.car_make_model.is_required | boolean | Whether or not the input for the client's car make and model should be required
intake.intake_form_override.sms_opt_in | object | SMS opt-in customization
intake.intake_form_override.sms_opt_in.is_visible | boolean | Whether or not the input for the client's SMS opt-in should be shown
intake.intake_form_override.sms_opt_in.is_required | boolean | Whether or not the input for the client's SMS opt-in should be required
intake.intake_form_override.media_opt_in | object | Social media customization
intake.intake_form_override.media_opt_in.is_visible | boolean | Whether or not the input for if the vet is allowed to use pictures of the client's pet on social media should be shown
intake.intake_form_override.media_opt_in.is_required | boolean | Whether or not the input for if the vet is allowed to use pictures of the client's pet on social media should be required

## Get configuration (admin)

> Response example

```json
{
  "account": {
    "email": "info@scoutvet.com",
    "pawprint_email": "demo2@snoutid.com"
  },
  "place": {
    "name": "Super Test Veterinary Clinic",
    "shortname": "Super Test Vet",
    "banner_image": "https://api.scoutvet.com/images/logo-header.png"
    "address": "123 Main St, Bellevue, WA 98004",
    "phone": "+15555554567"
  },
  "confirmations": {
    "enabled": true
  },
  "reminders": {
    "enabled": true,
    "reminder_cadence": [
      {
        "email": true,
        "sms": true,
        "days": -1
      },
      {
        "email": true,
        "sms": false,
        "days": 7
      }
    ],
    "appointment_request_instructions": "What's this?",
    "appointment_website": "https://scoutvet.com/contact",
  },
  "sms_messaging": {
    "enabled": true,
    "twilio_phone_number": "+15555554567",
    "auto_reply": true,
    "auto_reply_message": "Thank you for contacting Super Test Vet. We normally return messages within 48 hours."
  },
  "intake": {
    "vanity_slug": "supertestvet",
    "terms_of_service": "I hereby agree to everything.",
    "intake_form_override": {
      "car_make_model": {
        "is_visible": true,
        "is_required": true
      },
      "user_birthdate": {
        "is_visible": true,
        "is_required": true
      },
      "user_address": {
        "is_visible": false,
        "is_required": false
      },
      "referral": {
        "is_visible": true,
        "is_required": false
      },
      "media_opt_in": {
        "is_visible": false,
        "is_required": false
      },
      "questionnaire_id": 4,
      "checkin_instructions": "Please wear a mask to comply with the governor's mandate.",
      "vetstoria": {
        "base_url": "https://nva.vestoria.com",
        "site_hash": "becf2349baf"
      }
    }
  }
}
```
Gets a vet configuration; some sections are read-only: `place.pawprint_email`, `intake.vanity_slug`, `sms_messaging.twilio_phone_number`

### HTTP Request
`GET /admin/vet/:place_id/settings`

## Update configuration (admin)
> Request example

```json
{
  "account": {
    "email": "info@scoutvet.com",
    "pawprint_email": "demo2@snoutid.com"
  },
  "place": {
    "name": "Super Test Veterinary Clinic",
    "address": "123 Main St, Bellevue, WA 98004",
    "phone": "+15555554567",
    "email": "info@scoutvet.com",
    "shortname": "Super Test Vet",
    "banner_image": "https://api.scoutvet.com/images/logo-header.png"
  },
  "confirmations": {
    "enabled": true
  },
  "reminders": {
    "enabled": true,
    "reminder_cadence": [
      {
        "email": true,
        "sms": true,
        "days": -1
      },
      {
        "email": true,
        "sms": false,
        "days": 7
      }
    ],
    "appointment_website": "https://scoutvet.com/contact",
  },
  "sms_messaging": {
    "enabled": true,
    "twilio_phone_number": "+15555554567",
    "auto_reply": true,
    "auto_reply_message": "Thank you for contacting Super Test Vet. We normally return messages within 48 hours."
  },
  "intake": {
    "datasync_intake": true,
    "active_client_intakes": true,
    "terms_of_service": "I hereby agree to everything.",
    "intake_form_override": {
      "car_make_model": {
        "is_visible": true,
        "is_required": true
      },
      "user_birthdate": {
        "is_visible": true,
        "is_required": true
      },
      "user_address": {
        "is_visible": false,
        "is_required": false
      },
      "referral": {
        "is_visible": true,
        "is_required": false
      },
      "media_opt_in": {
        "is_visible": false,
        "is_required": false
      },
      "questionnaire_id": 4,
      "checkin_instructions": "Please wear a mask to comply with the governor's mandate.",
      "vetstoria": {
        "base_url": "https://nva.vestoria.com",
        "site_hash": "becf2349baf"
      }
    }
  }
}
```

> Response example

```json
(Same as get admin configuration)
```
Updates a vet configuration; sections may be partially specified.

### HTTP Request
`PATCH /admin/vet/:place_id/settings`

### PATCH parameters
Parameter | Type | Description
--------- | ---- | -----------
account | object? | Account information for SnoutID emails and banner image
account.email | string | Login email
place | object? | Place information for email, phone and physical contact
place.name | string | Place's official name
place.shortname | string |  Place's short name, used in SMS messages
place.address | string | Place's physical address
place.phone | string | Place's phone number
confirmations | object? | Appointment confirmation settings
confirmations.enabled | boolean | Feature on/off
reminders | object? | Reminder settings
reminders.enabled | boolean | Feature on/off
reminders.appointment_website | string? | Booking link in reminder email
reminders.reminder_cadence | object[] | Reminder schedule
reminders.reminder_cadence.email | boolean | Whether or not an email reminder should be sent (if possible)
reminders.reminder_cadence.sms | boolean | Whether or not an SMS reminder should be sent (if possible)
reminders.reminder_cadence.days | integer | Days from expiration the reminder should be sent; positive numbers are before the due date and negative numbers are after the due date.
sms_messaging | object? | 2-way text settings; vets will always get a twilio_phone number, but must contact us to enable/disable 2-way text
sms_messaging.enabled | boolean | Feature on/off
sms_messaging.auto_reply | boolean | Whether or not to send an automatic reply to the client's first incoming text
sms_messaging.auto_reply_message | string | The automatic reply to the client's first incoming text
intake | object? | Intake settings
intake.datasync_intake | boolean | Whether or not to generate intakes from the `vet_schedule` table
intake.active_client_intakes | boolean | Whether or not to generate intakes for clients that had an appointment in the past 6 months
intake.intake_form_override | object | Intake form customizations
intake.intake_form_override.user_birthdate | object | Birthdate customization
intake.intake_form_override.user_birthdate.is_visible | boolean | Whether or not the input for the client's birthdate should be shown
intake.intake_form_override.user_birthdate.is_required | boolean | Whether or not the input for the client's birthdate should be required
intake.intake_form_override.address | object | Client address customization
intake.intake_form_override.address.is_visible | boolean | Whether or not the input for the client's address should be shown
intake.intake_form_override.address.is_required | boolean | Whether or not the input for the client's address should be required
intake.intake_form_override.referral | object | Referral customization
intake.intake_form_override.referral.is_visible | boolean | Whether or not the input for how the client found the vet should be shown
intake.intake_form_override.referral.is_required | boolean | Whether or not the input for how the client found the vet should be required
intake.intake_form_override.car_make_model | object | Car make and model customization
intake.intake_form_override.car_make_model.is_visible | boolean | Whether or not the input for the client's car make and model should be shown
intake.intake_form_override.car_make_model.is_required | boolean | Whether or not the input for the client's car make and model should be required
intake.intake_form_override.sms_opt_in | object | SMS opt-in customization
intake.intake_form_override.sms_opt_in.is_visible | boolean | Whether or not the input for the client's SMS opt-in should be shown
intake.intake_form_override.sms_opt_in.is_required | boolean | Whether or not the input for the client's SMS opt-in should be required
intake.intake_form_override.media_opt_in | object | Social media customization
intake.intake_form_override.media_opt_in.is_visible | boolean | Whether or not the input for if the vet is allowed to use pictures of the client's pet on social media should be shown
intake.intake_form_override.media_opt_in.is_required | boolean | Whether or not the input for if the vet is allowed to use pictures of the client's pet on social media should be required
intake.questionnaire_id | integer? | `null` to disable questionnaires, otherwise a foreign reference to `form.id`.
vetstoria | object? | Vetstoria configuration
vetstoria.base_url | string | Vetstoria base URL
vetstoria.site_hash | string | Vetstoria site hash