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
    "address": "123 Main St, San Francisco, CA 94302"
  },
  "pet": {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix",
    "birthdate": "2018-02-16"
  },
  "screening": {
    "is_new_client": true
  },
	"appointment": {
    "place": 27015,
    "date": "2019-07-20",
    "time": "15:30",
    "timezone": "America/Los_Angeles",
    "type": "Wellness Exam",
    "reason": "Just moved here from Chicago"
  },
  "banner_image": "https://s3.aws.amazon.com/pawprint/pawprint-images/partner_logo.jpg",
  "status": "pending user"
}
```

Gets the specified intake form. Not all fields are guaranteed to be populated, based on what the vet filled out when creating
the intake form.

### HTTP Request
`GET /intake/:intake_id`

## Get intake bundle (user)

> Response example

```json
{
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, San Francisco, CA 94302"
  },
  "appointment": {
    "place": 27015,
    "date": "2019-07-20",
    "type": "Wellness Exam",
    "reason": "Just moved here from Chicago"
  },
  "banner_image": "https://s3.aws.amazon.com/pawprint/pawprint-images/partner_logo.jpg",
  "screening": {
    "is_new_client": true
  },
  "intakes": [
    {
      "external_id": "aXclIS89",
      "pet": {
        "name": "Milo",
        "species": "dog",
        "breed": "Australian Cattle Dog Mix",
        "birthdate": "2018-02-16"
      }
    },
    {
      "external_id": "OOX-09_HL",
      "pet": {
        "name": "Pumpkin",
        "species": "Cat",
        "breed": "Domestic Shorthair",
        "birthdate": "2009-10-31"
      }
    }
  ]
}
```

Gets multiple intake forms. Bundles are predefined on the server. A bundle can contain one or more intakes, and each intake can only belong to one bundle. Client and appointment information will be consolidated. Bundles are formed based on combined client email + phone (including nulls) and appointment date. Consequently, all intakes in a bundle will have the same client email, phone and appointment date (but the times may be different).

### HTTP Request
`GET /intake/b/:bundle_id`

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
    "birthdate": "1990-01-01"
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
    "type": "Outdoor"
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
user | object | The new client.
user.first_name | string | New client's first name.
user.last_name | string | New client's last name.
user.email | string | New client's email address.
user.phone | string | New client's phone number.
user.address | string? | New client's address.
user.birthdate | datetime? | New client's birthdate (for DEA reporting of prescriptions of controlled substances)
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

Gets payment terms and terms of service copy for a particular vet.

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
  }
}
```

Gets information available to the general public about a place, for the purpose of creating intakes. The `:place_id` URL parameter can be either the place's ID or the `partner_place.vanity_slug`.

Also contains configuration for place-specific form overrides. Current overridable fields are `address`, `user_birthdate`, `referral`, `media_opt_in`, and `sms_opt_in`. Current overridable settings are `is_required` and `is_visible`.

### HTTP Request
`GET /place/:place_id/intake`

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
    "birthdate": "1990-01-01"
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
        "neuter": true
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
      ]
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
{
  "Milo": "eXIjl3poE05z8vvvJqaofn",
  "Pumpkin": "98cgckxlf3DDmf9AFjv4089pz"
}
```

This creates an intake form all in one go, without the need for action on the vet side.
Appointments can come in two forms: the `booking.appointment` object and the `appointment_request` object.
The difference between them is that `booking.appointment` should be used when the appointment has already been set up, whereas `appointment_request` is simply to connect the client with the vet so they can agree upon a specific appointment time.
Setting `booking.appointment` will apply a due date to the intake and associated record requests and make it eligible for reminders, while the information in `appointment_request` is used solely for sending an email to the vet and/or client, and nothing else.

### HTTP Request
`POST /intake/place/:place_id`

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