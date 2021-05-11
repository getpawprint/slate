# Intake forms
Intake forms are for vets who are taking on new clients. The vet enters basic information about the new client,
such as name, email address and pet name into a form, then submits the form. An external ID is created for the partially
completed form, and then a link is emailed to the new client. When the client clicks the link, they will see another form with
the partially complete information that the vet entered. They should then enter the rest of the information about the pet,
such as species, date of birth, and most importantly, the other vets in the pet's medical history. 
Once they have completed the form, they can submit it, which causes record requests to be created in the Pawprint system.
When updated or completed, the records are emailed to both the client and their new vet.

Identity and authentication is based on a JWT given to the records portal when a vet has logged in; it is based on the vet's `place_id`.

## Create intake form (vet)
> Request example

```json
{
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, San Francisco, CA 94302"
  },
  "pet": {
    "name": "Milo",
    "species": "Dog"
  },
	"appointment": {
    "date": "2019-07-20",
    "time": "15:30"
  }
}
```

> Response example

```json
{ "id": "eYJnm8909C-A90xCMpznzC" }
```

Initializes an intake form with the given information. Extra fields can be passed to the endpoint,
but only a small subset is necessary for creation. An external ID for the form is generated and emailed to the new client,
which can be used to fill in the rest of the fields. The external ID is also returned by the endpoint.

### HTTP Request
`POST /partners/intake`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
user | object | The new client.
user.first_name | string | New client's first name.
user.last_name | string | New client's last name.
user.email | string | New client's email address.
user.phone | string? | New client's phone number.
user.address | string? | New client's address.
pet | object | The pet to be seen.
pet.name | name | Pet's name.
pet.species | string | Pet's species, e.g. `Cat` or `Dog`.
pet.breed | string? | Pet's breed.
pet.birthdate | date? | Date portion of the pet's birthdate
appointment | object | Appointment details
appointment.date | string | Date portion of the appointment, e.g. "2019-07-20".
appointment.time | string? | Time portion of the appointment in 24 hour time, e.g. "15:30" or "09:45".
appointment.timezone | string? | https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
appointment.type | string? | Broad category; see `appointment_type` table in database
appointment.reason | string? | More details about why this appointment is being made

## Get intake form (user)

> Response example

```json
{
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, San Francisco, CA 94302",
    "car_make_model": "Grey Subaru Outback"
  },
  "pet": {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix",
    "birthdate": "2018-02-16",
    "insurance": {
      "name": "HealthyPaws",
      "policy_number": "A00097"
    }
  },
  "screening": {
    "is_new_client": true,
    "is_active_client": true
  },
	"appointment": {
    "place": 27015,
    "date": "2019-07-20",
    "time": "15:30",
    "timezone": "America/Los_Angeles",
    "type": "Wellness Exam",
    "reason": "Just moved here from Chicago"
  },
  "checkin": {
    "checked_in_at": "2020-01-20T16:30:12Z",
    "location": "Next to the handicap spot"
  },
  "form_signature_required": false,
  "banner_image": "https://s3.aws.amazon.com/pawprint/pawprint-images/partner_logo.jpg",
  "status": "pending user",
  "previous_vets": [
    {
      "id": 27015,
      "name": "Pawprint Test Vet",
      "address": "814 Mission St, San Francisco, CA 94302",
      "city": "San Francisco",
      "state": "CA"
    }
  ],
  "questionnaire": {
    "name": "pet_questionnaire",
    "per": "pet",
    "modules": [{
      "label": "Appointment Details",
      "questions": [{
        "name": "label1",
        "label": "We are seeing Bowser for: Wellness Exam",
        "input_type": "label"
      },
      {
        "name": "other_concerns",
        "label": "Do you have any other concerns you'd like to address?",
        "input_type": "textarea"
      }]
    },
    {
      "label": "Medical",
      "questions": [{
        "name": "allergies",
        "label": "Please list your pet's allergies, if any",
        "input_type": "text"
      },
      {
        "name": "diet",
        "label": "Diet: Please list everything your pet will eat in a typical day, from treats to meals",
        "input_type": "text"
      },
      {
        "name": "medications",
        "input_type": "medications"
      }]
    }]
  },
  "form_instances": [
    {
      "external_id": "3zcdBF",
      "status": "pending user",
      "created_at": "2020-08-22T20:32:14Z",
      "updated_at": "2020-08-22T20:34:34Z",
      "content": {
        "name": "Jacksonville Surgery",
        "per": "pet",
        "modules": [
          {
            "label": "Description",
            "is_visible": true,
            "questions": [{
              "name": "label1",
              "label": "Vivamus sagittis lacus vel augue laoreet rutrum faucibu.",
              "input_type": "label",
              "response": null
            }],
          },
          {
            "label": "Signature",
            "is_visible": true,
            "questions": [{
              "name": "Lorem Ipsum",
              "label": "Cras mattis consectetur purus sit amet fermentum.",
              "input_type": "signature",
              "filled_by": "client",
              "response": null
            }]
          }
        ]
      }
    }
  ],
  "records": [{
    "request_id": 44326,
    "place": {
      "id": 27015,
      "name": "Pawprint Test Vet",
      "address": "814 Mission St, San Francisco, CA 94302",
      "phone": "(650) 555-5551",
      "website": "https://snoutid.com/"
    },
    "files": [{
      "title": "Medical History 2020",
      "url": "https://s3.aws.amazon.com/snoutid/snoutid-records/record_129389.pdf",
      "created_at": "2020-11-01T10:36:12.123Z"
    }]
  }]
}
```

Gets the specified intake form. Not all fields are guaranteed to be populated, based on what the vet filled out when creating
the intake form. The `questionnaire` field may not be present if there is more than 48 hours before the appointment;
full example of a question can be found at https://pawprint.slite.com/app/channels/6M2TLvSBWU/notes/peYMA8k23x.

### HTTP Request
`GET /intake/:external_id`

## Get intake form by confirmation code

> Response example

```json
(see above)
```

Same as getting an intake by its external ID, except that the route takes a `place_id` and `confirmation_code` pair instead of `external_id`.
Confirmation codes are separate IDs that are shorter than external IDs, are not globally unique by themselves.
They are generated for datasync intakes, and erased when the intake is submitted, completed, cancelled or expired.

### HTTP Request
`GET /intake/code/:place_id/:confirmation_code`

## Get intake bundle (user)

> Response example

```json
{
  "name": "Pawprint Farm",
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, San Francisco, CA 94302",
    "has_payment_method": true,
    "car_make_model": "Grey Subaru Outback"
  },
  "banner_image": "https://s3.aws.amazon.com/pawprint/pawprint-images/partner_logo.jpg",
  "screening": {
    "is_new_client": true,
    "is_active_client": true
  },
  "form_signature_required": false,
  "checkin": {
    "checked_in_at": "2020-01-20T16:30:12Z",
    "location": "Next to the handicap spot"
  },
  "bookings": [
    {
      "external_id": "aXclIS89",
      "status": "pending user",
      "pet": {
        "name": "Milo",
        "species": "dog",
        "breed": "Australian Cattle Dog Mix",
        "birthdate": "2018-02-16",
        "insurance": {
          "name": "HealthyPaws",
          "policy_number": "A00097"
        }
      },
      "appointment": {
        "date": "2019-07-20",
        "time": "4:15 pm",
        "type": "Wellness Exam",
        "reason": "Just moved here from Chicago"
      },
      "form_instances": [
        {
          "id": 12,
          "name": "Jacksonville Surgery",
          "per": "pet",
          "status": "pending user",
          "modules": [
            {
              "label": "Description",
              "is_visible": true,
              "questions": [
                {
                  "name": "label1",
                  "label": "Vivamus sagittis lacus vel augue laoreet rutrum faucibu.",
                  "input_type": "label",
                  "response": null
                }
              ],
              
            },
            {
              "label": "List Menu",
              "is_visible": true,
              "questions": [
                {
                  "name": "allergies",
                  "label": "Lorem Ipsum Menu Title",
                  "input_type": "checkbox",
                  "options": [
                    "Option 1",
                    "Option 2",
                    "Option 3",
                    "Option 4"
                  ],
                  "filled_by": "client",
                  "response": null
                }
              ]
            },
            {
              "label": "Single-option selector",
              "is_visible": false,
              "questions": [
                {
                  "name": "Lorem Ipsum",
                  "label": "Cras mattis consectetur purus sit amet fermentum.",
                  "input_type": "radio",
                  "options": [
                    "Accept",
                    "Decline"
                  ],
                  "filled_by": "client",
                  "response": null
                }
              ]
            },
            {
              "label": "Single-option selector",
              "is_visible": true,
              "questions": [
                {
                  "name": "Lorem Ipsum",
                  "label": "Cras mattis consectetur purus sit amet fermentum.",
                  "input_type": "radio",
                  "options": [
                    "Accept",
                    "Decline"
                  ],
                  "filled_by": "vet",
                  "response": "Accept"
                }
              ]
            },
            {
              "label": "Signature",
              "is_visible": true,
              "questions": [
                {
                  "name": "Lorem Ipsum",
                  "label": "Cras mattis consectetur purus sit amet fermentum.",
                  "input_type": "signature",
                  "filled_by": "client",
                  "response": null
                },
                {
                  "name": "Name",
                  "label": null,
                  "input_type": "text",
                  "filled_by": "client",
                  "response": null
                },
                {
                  "name": "Date",
                  "label": null,
                  "input_type": "completed_date",
                  "response": null
                }
              ]
            }
          ]
        }
      ],
      "questionnaire": {
        "name": "pet_questionnaire",
        "per": "pet",
        "modules": [
          {
            "label": "Appointment Details",
            "questions": [
              {
                "name": "label1",
                "label": "We are seeing Pumpkin for: Wellness Exam",
                "input_type": "label"
              },
              {
                "name": "other_concerns",
                "label": "Do you have any other concerns you'd like to address?",
                "input_type": "textarea"
              }
            ]
          },
          {
            "label": "Medical",
            "questions": [
              {
                "name": "allergies",
                "label": "Please list your pet's allergies, if any",
                "input_type": "text"
              },
              {
                "name": "diet",
                "label": "Diet: Please list everything your pet will eat in a typical day, from treats to meals",
                "input_type": "text"
              },
              {
                "name": "medications",
                "input_type": "medications"
              }
            ]
          }
        ]
      },
      "records": [{
        "request_id": 44326,
        "place": {
          "id": 27015,
          "name": "Pawprint Test Vet",
          "address": "814 Mission St, San Francisco, CA 94302",
          "phone": "(650) 555-5551",
          "website": "https://snoutid.com/"
        },
        "files": [{
          "title": "Medical History 2020",
          "url": "https://s3.aws.amazon.com/snoutid/snoutid-records/record_129389.pdf",
          "created_at": "2020-11-01T10:36:12.123Z"
        }]
      }]
    },
    {
      "external_id": "OOX-09_HL",
      "status": "pending user",
      "pet": {
        "name": "Pumpkin",
        "species": "Cat",
        "breed": "Domestic Shorthair",
        "birthdate": "2009-10-31",
        "insurance": {
          "name": "HealthyPaws",
          "policy_number": "A00097"
        }
      },
      "appointment": {
        "date": "2019-07-20",
        "time": "4:30 pm",
        "type": "Wellness Exam",
        "reason": "Just moved here from Chicago"
      },
      "form_instances": [],
      "questionnaire": {
        "name": "pet_questionnaire",
        "per": "pet",
        "modules": [
          {
            "label": "Appointment Details",
            "questions": [
              {
                "name": "label1",
                "label": "We are seeing Pumpkin for: Wellness Exam",
                "input_type": "label"
              },
              {
                "name": "other_concerns",
                "label": "Do you have any other concerns you'd like to address?",
                "input_type": "textarea"
              }
            ]
          },
          {
            "label": "Medical",
            "questions": [
              {
                "name": "allergies",
                "label": "Please list your pet's allergies, if any",
                "input_type": "text"
              },
              {
                "name": "diet",
                "label": "Diet: Please list everything your pet will eat in a typical day, from treats to meals",
                "input_type": "text"
              },
              {
                "name": "medications",
                "input_type": "medications"
              }
            ]
          }
        ]
      },
      "records": [{
        "request_id": 44327,
        "place": {
          "id": 3,
          "name": "Super Test Vet",
          "address": "1500 145th Ave NE, San Francisco, CA 94302",
          "phone": "(650) 555-5555",
          "website": "https://demo.snoutid.com/super"
        },
        "files": [{
          "title": "Medical History 6/5/2020",
          "url": "https://s3.aws.amazon.com/snoutid/snoutid-records/record_a3kXj.pdf",
          "created_at": "2020-11-01T10:36:12.123Z"
        },
        {
          "title": "Medical History 6/5/2020 (2)",
          "url": "https://s3.aws.amazon.com/snoutid/snoutid-records/record_kk21e.pdf",
          "created_at": "2020-11-01T10:36:17.123Z"
        }]
      }]
    }
  ],
  "previous_vets": [
    {
      "id": 27015,
      "name": "Pawprint Test Vet",
      "address": "814 Mission St, San Francisco, CA 94302",
      "city": "San Francisco",
      "state": "CA"
    },
    {
      "id": 3,
      "name": "Super Test Vet",
      "address": "1500 145th Ave NE, San Francisco, CA 94302",
      "city": "San Francisco",
      "state": "CA"
    }
  ],
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
  },
  "charge": {
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
        "notes": null,
        "created_at": "2021-03-21T12:31:55-04:00:00"
      },
      {
        "amount": 2345,
        "method": "card",
        "status": "complete",
        "notes": null,
        "created_at": "2021-03-21T12:31:55-04:00:00"
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
      "id": 1000052,
      "first_name": "John",
      "last_name": "Smith"
    },
    "files": [{
      "description": "Invoice for PUMPKIN EXAM",
      "url": "https://snout-vet-invoice.s3-us-west-2.amazonaws.com/2394872834.pdf"
    }]
  }
}
```

Gets multiple intake forms. Bundles are predefined on the server. A bundle can contain one or more intakes, and each intake can only belong to one bundle. Bundles are formed based on combined client email + phone (including nulls) and appointment date. Consequently, all intakes in a bundle will have the same client email, phone and appointment date (but the times may be different).

The `questionnaire` field may not be present if there is more than 48 hours before the appointment;
full example of a question can be found at https://pawprint.slite.com/app/channels/6M2TLvSBWU/notes/peYMA8k23x.

The `payment_instruments` fields will be empty if the intake hasn't been submitted yet.

### HTTP Request
`GET /intake/bundle/:bundle_id`

## Get intake bundle by confirmation code

> Response example

```json
(see above)
```

Same as getting a set of intakes by its bundle ID, except that the route takes a `place_id` and `confirmation_code` pair instead of `bundle_id`.
All intakes in the same bundle have the same confirmation code.

### HTTP Request
`GET /intake/bundle/:place_id/:confirmation_code`

## Update/submit intake form (user)
> Request example

```json
{
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, Palo Alto, CA 94305",
    "birthdate": "1990-01-01",
    "car_make_model": "Ford Taurus, grey",
    "stripe_payment_method_id": "pm_czX1jil"
  },
  "secondary": {
    "first_name": "Jane",
    "last_name": "Smith",
    "phone": "555-555-6666"
  },
  "screening": {
    "is_new_client": true,
    "has_been_elsewhere": false,
    "has_been_vaccinated": true
  },
  "pet": {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix",
    "birthdate": "2018-02-16",
    "birthdate_level": "day",
    "neuter": true,
    "type": "Outdoor",
    "insurance": {
      "insurance_id": 6,
      "policy_number": "A0097"
    }
  },
  "marketing": {
    "media_opt_in": false,
    "sms_opt_in": false,
    "referral": "Word of mouth"
  },
  "place_ids": [ 27015, 2101 ],
  "vets": [
    {
      "name": "Pawprint Seattle",
      "city": "Seattle",
      "state": "WA",
      "zip": "98105"
    },
  ],
  "empty_reason": "New puppy/kitten",
  "note": "Milo used to be called Olim",
  "signature": "https://s3.aws.amazon.com/pawprint/pawprint-intake-signatures/eXa8nzcsSp_sig.png",
  "files": [
    {
      "name": "Medical History",
      "data": "image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVQYV2NgYAAAAAMAAWgmWQ0AAAAASUVORK5CYII="
    },
    {
      "name": "Jellystone records",
      "data": "image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVQYV2NgYAAAAAMAAWgmWQ0AAAAASUVORK5CYII="
    }
  ]
}
```

> Response example

```json
(none)
```

Updates an intake form with the given information. If one of the required fields is empty, HTTP 400/Bad Request is returned. One of `place_ids` or `vets` must be specified; both are accepted. Objects in the `vets` list will be created as new entries in the `place` table. If all of the required fields are populated, HTTP 200/OK is returned, and the record requests are created automatically.

After an intake form has been submitted, it cannot be submitted again, and HTTP 400/Bad Request is returned. This is because the
record requests are created as soon the intake form has been submitted the first time. If `intake.status` from `GET /intake/:external_id` is anything but `pending user`, the user should be blocked from submitting the request again.

### HTTP Request
`PUT /intake/:intake_id`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
user | object | Client.
user.first_name | string | Client's first name.
user.last_name | string | Client's last name.
user.email | string | Client's email address.
user.phone | string | Client's phone number.
user.address | string? | Client's address.
user.birthdate | datetime? | Client's birthdate (for DEA reporting of prescriptions of controlled substances)
user.car_make_model | string? | Client's vehicle information (for curbside operation)
user.stripe_payment_method_id | string? | Payment method ID (obtained from Stripe call)
secondary | object? | Secondary contact information, like a spouse
secondary.first_name | Secondary contact's first name.
secondary.last_name | Secondary contact's last name.
secondary.phone | Secondary contact's phone.
screening | object | Screening questions
screening.is_new_client | bool | Answers "Are you a new client?"
screening.has_been_elsewhere | bool | Answers "Have you been to any other vets?"
screening.has_been_vaccinated | bool? | Answers "Have any of your pets been vaccinated before?" (only relevant if `has_been_elsewhere` was false)
pet | object | The pet to be seen.
pet.name | name | Pet's name.
pet.species | string | Pet's species, e.g. `cat` or `dog`.
pet.breed | string | Pet's breed.
pet.birthdate | date? | Date portion of the pet's birthdate
pet.birthdate_level | string? | The accuracy of the pet's birthdate, e.g. if user only chose a `year`, a year and a `month`, or a complete date (`day`).
pet.gender | string | "m" or "f"
pet.neuter | bool? | Whether or not the pet has been neutered/spayed.
pet.type | string? | Cats only - "Indoor only", "Outdoor only" or "Both"
pet.insurance | object? | Pet's insurance information
pet.insurance.insurance_id | integer | ID from insurance table (call `GET /insurance` for a list)
pet.insurance.policy_number | string | Insurance policy number
marketing | object? | Marketing fields for vet (no effect on intake)
marketing.media_opt_in | bool | Does the client release rights to clinic to use photos for social
marketing.sms_opt_in | bool | Does the client opt into text
marketing.referral | string | How you heard about the clinic - string (include a couple default options like yelp, google, friend)
place_ids | int[]? | List of IDs from the `place` table who we will contact for the pet's medical history. If `place_ids` and `vets` are not specified, `empty_reason` must be given.
vets | object[]? | Vets who we will contact for the pet's medical history. These will become new `place` table entries. If `place_ids` and `vets` are not specified, `empty_reason` must be given.
vets.name | string | Vet name.
vets.city | string? | Vet's city.
vets.state | string? | Vet's state.
vets.zip | string? | Vet's zip code.
empty_reason | string? | If `place_ids` and `vets` are not specified, this is the reason why - e.g., "I forgot which vets my pet has seen" or "New puppy/kitten". Otherwise, this string is ignored.
note | string? | Note to the vets.
signature | string | Signature of the user's consent.
files | object[]? | User uploaded files
files.name | string | User-submitted description of the file
files.data | string | base64 encoded file

## Get place terms of service

> Response example

```json
{
  "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
}
```

Gets payment terms and terms of service copy for a particular vet. May include HTML.

### HTTP Request
`GET /place/:place_id/tos`

## Get place info

> Response example

```json
{
  "name": "Jellystone Animal Hospital",
  "banner_image": "https://s3.aws.amazon.com/pawprint/pawprint-images/partner_logo.jpg",
  "place_id": 9001,
  "appointment_request_enabled": true,
  "appointment_types": [
    {
      "id": 1,
      "description": "Wellness exam"
    },
    {
      "id": 2,
      "description": "Illness/injury"
    }
  ],
  "overrides": {
    "address": {
      "is_required": true,
      "is_visible": true
    },
    "user_birthdate": {
      "is_required": true,
      "is_visible": true
    },
    "referral": {
      "is_required": false,
      "is_visible": true
    },
    "media_opt_in": {
      "is_required": false,
      "is_visible": false
    },
    "sms_opt_in": {
      "is_required": false,
      "is_visible": false
    }
  },
  "checkin_instructions": "Please wait in the parking lot and call 555-555-5555 when you arrive.",
  "timezone": "America/Los_Angeles"
}
```

Gets information available to the general public about a place, for the purpose of creating intakes. The `:place_id` URL parameter can be either the place's ID or the `partner_place.vanity_slug`.

Also contains configuration for place-specific form overrides. Current overridable fields are `address`, `user_birthdate`, `referral`, `media_opt_in`, and `sms_opt_in`. Current overridable settings are `is_required` and `is_visible`.

### HTTP Request
`GET /v2/place/:place_id/intake`

## Create complete intake form (user)
> Request example

```json
{
  "signature": "base64:jpeg,hbIL8cqGoxa3w3",
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, Palo Alto, CA 94305",
    "birthdate": "1990-01-01",
    "car_make_model": "Ford Taurus, grey",
    "stripe_payment_method_id": "pm_czX1jil"
  },
  "secondary": {
    "first_name": "Jane",
    "last_name": "Smith",
    "phone": "555-555-6666"
  },
  "screening": {
    "is_new_client": true,
    "has_been_elsewhere": false,
    "has_been_vaccinated": true
  },
  "marketing": {
    "media_opt_in": false,
    "sms_opt_in": false,
    "referral": "Word of mouth"
  },
  "bookings": [
    {
      "pet": {
        "name": "Milo",
        "species": "dog",
        "breed": "Australian Cattle Dog Mix",
        "birthdate": "2018-02-16",
        "profile_pic": "image/jpeg;base64:jpeg,Ax08uoawnyCAOA-Muaw3==",
        "gender": "m",
        "neuter": true,
        "insurance": {
          "insurance_id": 6,
          "policy_number": "A0097"
        }
      },
      "place_ids": [ 27015, 2101 ],
      "vets": [
        {
          "name": "Pawprint Seattle",
          "city": "Seattle",
          "state": "WA",
          "zip": "98105"
        }
      ],
      "empty_reason": "New puppy",
      "appointment": {
        "date": "2019-07-09",
        "time": "1:30 PM",
        "timezone": "America/Los_Angeles",
        "type": "Wellness Exam",
        "reason": "Just moved to town"
      },
      "note": "Milo used to be called Olim",
      "files": [
        {
          "name": "Medical History",
          "data": "image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVQYV2NgYAAAAAMAAWgmWQ0AAAAASUVORK5CYII="
        },
        {
          "name": "Jellystone records",
          "data": "image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVQYV2NgYAAAAAMAAWgmWQ0AAAAASUVORK5CYII="
        }
      ],
    },
    {
      "pet": {
        "name": "Pumpkin",
        "species": "cat",
        "breed": "Domestic Short Hair",
        "birthdate": "2009-07-11",
        "profile_pic": "base64:jpeg,9ua-v9XASef2-=",
        "gender": "f",
        "neuter": false,
        "type": "Indoor only"
      },
      "place_ids": [ 2101 ],
      "vets": [
        {
          "name": "Jellystone Animal Hospital",
          "city": "Jackson",
          "state": "WY",
          "zip": "83002"
        },
      ],
      "empty_reason": "New kitten",
      "appointment": {
        "date": "2019-07-09",
        "time": "1:30 PM",
        "timezone": "America/Los_Angeles",
        "type": "Illness/injury",
        "reason": "Persistent cough"
      },
      "note": "Doesn't like being touched on the head",
      "files": [
        {
          "name": "Medical History",
          "data": "image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVQYV2NgYAAAAAMAAWgmWQ0AAAAASUVORK5CYII="
        },
        {
          "name": "Jellystone records",
          "data": "image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAQAAAC1HAwCAAAAC0lEQVQYV2NgYAAAAAMAAWgmWQ0AAAAASUVORK5CYII="
        }
      ]
    }
  ],
  "appointment_request": {
    "date": "2019-08-30",
    "times": [ "8:00 AM-11:00 AM", "11:00 AM-1:00 PM" ],
    "type": "New client",
    "reason": "Persistent cough"
  }
}
```

> Response example

```json
(v1)
{
  "Milo": "eXIjl3poE05z8vvvJqaofn",
  "Pumpkin": "98cgckxlf3DDmf9AFjv4089pz"
}

(v2)
{
  "bundle_id": "asdf",
  "intake_ids": {
    "Milo": "eXIjl3poE05z8vvvJqaofn",
    "Pumpkin": "98cgckxlf3DDmf9AFjv4089pz"
  }
}
```

This creates an intake form all in one go, without the need for action on the vet side.
Appointments can come in two forms: the `booking.appointment` object and the `appointment_request` object.
The difference between them is that `booking.appointment` should be used when the appointment has already been set up, whereas `appointment_request` is simply to connect the client with the vet so they can agree upon a specific appointment time.
Setting `booking.appointment` will apply a due date to the intake and associated record requests and make it eligible for reminders, while the information in `appointment_request` is used solely for sending an email to the vet and/or client, and nothing else.

The optional `realtime` query parameter will mark the intake as a realtime intake, which increases its priority and immediately notifies an admin to process it.

### HTTP Request
`POST [v2]/intake/place/:place_id[?realtime=true]`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
user | object | The new client.
user.first_name | string | New client's first name.
user.last_name | string | New client's last name.
user.email | string | New client's email address.
user.phone | string | New client's phone number.
user.address | string | New client's address.
user.birthdate | datetime? | New client's birthdate (for DEA reporting of prescriptions of controlled substances)
user.car_make_model | string? | Client's vehicle information (for curbside operation)
user.stripe_payment_method_id | string? | Payment method ID (obtained from Stripe call)
secondary | object? | Secondary contact information, like a spouse
secondary.first_name | Secondary contact's first name.
secondary.last_name | Secondary contact's last name.
secondary.phone | Secondary contact's phone.
screening | object | Screening questions
screening.is_new_client | bool | Answers "Are you a new client?"
screening.has_been_elsewhere | bool | Answers "Have you been to any other vets?"
screening.has_been_vaccinated | bool? | Answers "Have any of your pets been vaccinated before?" (only relevant if `has_been_elsewhere` was false)
marketing | object? | Marketing fields for vet (no effect on intake)
marketing.media_opt_in | bool | Does the client release rights to clinic to use photos for social
marketing.sms_opt_in | bool | Does the client opt into text
marketing.referral | string | How you heard about the clinic - string (include a couple default options like yelp, google, friend)
bookings | object[] | Contains information about each pet and appointment
bookings.pet | object | The pet to be seen.
bookings.pet.name | name | Pet's name.
bookings.pet.species | string | Pet's species, e.g. `cat` or `dog`.
bookings.pet.breed | string? | Pet's breed.
bookings.pet.birthdate | date? | Date portion of the pet's birthdate
bookings.pet.gender | string | "m" or "f"
bookings.pet.neuter | bool? | Whether or not the pet has been neutered/spayed.
bookings.pet.profile_pic | base64? | Base64 encoded image of the pet
bookings.pet.type | string? | Cats only - "Indoor only", "Outdoor only" or "Both"
bookings.pet.insurance | object? | Pet's insurance information
bookings.pet.insurance.insurance_id | integer | ID from insurance table (call `GET /insurance` for a list)
bookings.pet.insurance.policy_number | string | Insurance policy number
bookings.place_ids | int[]? | List of IDs from the `place` table who we will contact for the pet's medical history. One of `place_ids` or `vets` must be specified, or else `empty_reason` must be given.
bookings.vets | object[]? | Vets who we will contact for the pet's medical history. These will become new `place` table entries. One of `place_ids` or `vets` must be specified, or else `empty_reason` must be given.
bookings.vets.name | string | Vet name.
bookings.vets.city | string? | Vet's city.
bookings.vets.state | string? | Vet's state.
bookings.vets.zip | string? | Vet's zip code.
bookings.empty_reason | string? | Reason for not having pet's medical history (if `bookings.place_ids` and `bookings.vets` are both empty)
bookings.appointment | object? | Appointment details
bookings.appointment.date | string? | Date portion of the appointment, e.g. "2019-07-20".
bookings.appointment.time | string? | Time portion of the appointment in 24 hour time, e.g. "15:30" or "09:45".
bookings.appointment.timezone | string? | https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
bookings.appointment.type | string? | Broad category; see `appointment_type` table in database
bookings.appointment.reason | string? | More details about why this appointment is being made
bookings.note | string? | Note to the vets.
bookings.files | object[]? | User uploaded files
bookings.files.name | string | User-submitted description of the file
bookings.files.data | string | Base64 encoded file
signature | string | Base64 encoded signature of the user's consent.
appointment_request | object? | Appointment request object; this is exclusively for sending to the vet and does not affect the intake in any way.
appointment_request.date | string? | Date portion of the appointment, e.g. "2019-07-20".
appointment_request.times | string[]? | Array of requested times; these are passed through the backend and sent to the vet directly.
appointment_request.type | string? | Broad category; see `appointment_type` table in database
appointment_request.reason | string? | More details about why this appointment is being made


## Update pet profile pic (user)
> Request example

```json
{
  "image": "base64:jpeg,hbIL8cqGoxa3w3"
}
```

> Response example

```json
(none)
```

Updates a pet's profile picture; this can be done at any stage of the intake process at and after `pending user`.

### HTTP Request
`POST /intake/:intake_id/image`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
image | string | Pet's new profile picture in base64 encoding.


## Update intake appointment (user)
> Request example

```json
{
	"appointment": {
    "date": "2019-07-20",
    "time": "15:30",
    "timezone": "America/Los_Angeles",
    "type": "Injury/Illness",
    "reason": "Mass on right hind leg"
  }
}
```

> Response example

```json
(none)
```

**Deprecated, please use `PATCH /intake/:intake_id`**
Updates the appointment on the intake; anyone with the link/intake external ID can update this.

### HTTP Request
`PUT /intake/:intake_id/appointment`

### PUT parameters
Parameter | Type | Description
--------- | ---- | -----------
appointment | object? | Appointment details
appointment.date | string? | Date portion of the appointment, e.g. "2019-07-20".
appointment.time | string? | Time portion of the appointment in 24 hour time, e.g. "3:30 pm" or "9:45 am".
appointment.timezone | string? | https://en.wikipedia.org/wiki/List_of_tz_database_time_zones.

## Update intake (user)
> Request example

```json
{
	"appointment": {
    "date": "2019-07-20",
    "time": "15:30",
    "timezone": "America/Los_Angeles",
    "type": "Injury/Illness",
    "reason": "Mass on right hind leg"
  },
  "pet": {
    "insurance": {
      "insurance_id": 1,
      "policy_number": "IPC013297"
    }
  },
  "insurance": {
    "interest": "interested"
  },
  "marketing": {
    "media_opt_in": true,
    "sms_opt_in": true,
    "referral": "Word of mouth"
  }
}
```

> Response example

```json
(none)
```

Updates the intake; anyone with the link/intake external ID can update certain parts of the submitted data.

### HTTP Request
`PATCH /intake/:intake_id`

### PATCH parameters
Parameter | Type | Description
--------- | ---- | -----------
appointment | object? | Appointment details
appointment.date | string? | Date portion of the appointment, e.g. "2019-07-20".
appointment.time | string? | Time portion of the appointment in 24 hour time, e.g. "3:30 pm" or "9:45 am".
appointment.timezone | string? | https://en.wikipedia.org/wiki/List_of_tz_database_time_zones.
pet | object? | Pet details
pet.insurance | object? | Pet's insurance info
pet.insurance.insurance_id | string | Insurance company from calling `GET /insurance`
pet.insurance.policy_number | string | Pet's insurance policy number
insurance | object? | User's interest in getting an insurance quote
insurance.interest | string | One of `interested` or `not now`
marketing | object? | Marketing details
marketing.media_opt_in | bool | Does the client release rights to clinic to use photos for social
marketing.sms_opt_in | bool | Does the client opt into text
marketing.referral | string | How you heard about the clinic - string (include a couple default options like yelp, google, friend)

## Send magic link
> Request example

```json
{
  "email":"johnsmith@getpawprint.com"
}
```

> Response example

```json
(none))
```

Sends a link to the given email; the email should be of an existing Pawprint user. See `GET /activation/email` to check if an email belongs to an existing Pawprint user. The link goes back to the intake form, but includes an auth token so that user and pet profile APIs can be called.

### HTTP Request
`POST /intake/:external_id/magic_link`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
email | string | Client's email

## Preview additional documents
> Request example

```json
{
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, Palo Alto, CA 94305"
  },
  "pets": [
    {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix"
    },
    {
    "name": "Pumpkin",
    "species": "cat",
    "breed": "Exotic Shorthair"
    }
  ]
}
```

> Response example

```json
[
  {
    "title": "Cancellation policy 2020",
    "text": "<div>
      <h2>Cancellation policy</h2>
      <p>No refunds for cancellations made less than 24 hours in advance.</p>
    </div>"
  },
  {
    "title": "Surgery form",
    "text": "<div>
      <h2>Surgery agreement for Milo</h2>
      <p>
        <input type=\"checkbox\" /><label>I understand animals may have an adverse reaction to anesthesia.</label><br />
        <input type=\"checkbox\" /><label>For spays and neuters, I understand that pets get a spay/neuter tattoo.</label>
      </p>
    </div>"
  }
]
```

Gets a preview of additional documents that the user must sign. The contents of the text field depends on the template used
to generate the document; each custom document for each vet has its own template.

If there are no additional documents, an empty array is returned.

This API is only for datasync bundles; standalone and Vetstoria intakes should only display the partner's standard Terms of Service.

### HTTP Request
`POST /intake/bundle/:bundle_id/documents`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
user | object | Client details
user.first_name | string | Client's first name.
user.last_name | string | Client's last name.
user.email | string | Client's email address.
user.phone | string | Cient's phone number.
user.address | string? | Client's address.
pets | object[] | List of patients
pets.name | name | Patient's name.
pets.species | string | Patient's species, e.g. `Cat` or `Dog`.
pets.breed | string? | Patient's breed.

## Save intake state (standalone and Vetstoria) and send verification
> Request example

```json
{
    "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, Palo Alto, CA 94305"
  },
  "pets": [
    {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix"
    },
    {
    "name": "Pumpkin",
    "species": "cat",
    "breed": "Exotic Shorthair"
    }
  ]
}
```

> Response example

```json
(none)
```

There is no response content; instead, the backend generates a secret verification link which is emailed to the user.

### HTTP Request
`POST /intake/state`

### POST parameters
Anything you want; the entire blob is saved to the database as is.

## Load intake state (standalone and Vetstoria)
> Response example

```json
{
    "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, Palo Alto, CA 94305"
  },
  "pets": [
    {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix"
    },
    {
    "name": "Pumpkin",
    "species": "cat",
    "breed": "Exotic Shorthair"
    }
  ]
}
```

The response content is the blob that was passed in the `POST /intake/state` call.
`:token` is generated in the `POST /intake/state` call and emailed to the user for verification.

### HTTP Request
`GET /intake/state/:token`


## Submit questionnaire answers
> Request example

```json
{
  "other_concerns": "n/a",  // textarea
  "allergies": "beef, dairy",  // text
  "diet": "grain free, Dr. Hill's I/D", // text
  "medications": [{"id": 42, "refill": true}, {"id": 37, "refill": false}],  // medications
  "activity_level": ["Park","Leash Walks"],    // checkboxes
  "other_pets": {"cats": 0, "dogs": 1},  // other_pets
  "thirst": "Same",    // select
  "urination": "Same",    // select
  "appetite": "Same",    // select
  "energy": "Same",    // select
  "vomiting": "No",    // select
  "diarrhea": "No"    // select
}
```

These are simply key value pairs based on the `questions[].name` field in the `questionnaire` object attached
to intake and bundle responses.

### HTTP Request
`POST /intake/:external_id/questionnaire`

## Check in for an appointment
> Request example

```json
{
  "car_make_model": "Grey Subaru Outback",
  "location": "Next to the handicap spot"
}
```

> Response example

```json
(none)
```

Logs a checkin timestamp and location for each intake in the bundle. If `car_make_model` is specified, it will update that information in the user profile.

### HTTP Request
`POST /intake/bundle/:bundle_id/checkin`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
car_make_model | string? | Car make & model if different from what we have on file
location | string? | Description of client's physical location
