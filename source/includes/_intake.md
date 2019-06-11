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
	"appointment": {
    "place": 27015,
    "date": "2019-07-20",
    "time": "15:30",
    "timezone": "America/Los Angeles",
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

## Update intake form (user)
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
  "pet": {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix",
    "birthdate": "2018-02-16"
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
  "note": "Milo used to be called Olim",
  "signature": "https://s3.aws.amazon.com/pawprint/pawprint-intake-signatures/eXa8nzcsSp_sig.png"
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
user.address | string | New client's address.
pet | object | The pet to be seen.
pet.name | name | Pet's name.
pet.species | string | Pet's species, e.g. `cat` or `dog`.
pet.breed | string | Pet's breed.
pet.birthdate | date? | Date portion of the pet's birthdate
place_ids | int[]? | List of IDs from the `place` table who we will contact for the pet's medical history. One of `place_ids` or `vets` must be specified.
vets | object[]? | Vets who we will contact for the pet's medical history. These will become new `place` table entries. One of `place_ids` or `vets` must be specified.
vets.name | string | Vet name.
vets.city | string? | Vet's city.
vets.state | string? | Vet's state.
vets.zip | string? | Vet's zip code.
note | string? | Note to the vets.
signature | string | Signature of the user's consent.

## Get place info

> Response example

```json
{
  "name": "Jellystone Animal Hospital",
  "banner_image": "https://s3.aws.amazon.com/pawprint/pawprint-images/partner_logo.jpg",
}
```

Gets information available to the general public about a place, for the purpose of creating intakes.

### HTTP Request
`GET /place/:place_id/intake`

## Create complete intake form (user)
> Request example

```json
{
  "signature": "https://s3.aws.amazon.com/pawprint/pawprint-intake-signatures/eXa8nzcsSp_sig.png"
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555",
    "address": "123 Main St, Palo Alto, CA 94305"
  },
  "bookings": [
    {
      "pet": {
        "name": "Milo",
        "species": "dog",
        "breed": "Australian Cattle Dog Mix",
        "birthdate": "2018-02-16",
        "profile_pic": "base64:jpeg,Ax08uoawnyCAOA-Muaw3=="
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
      "appointment": {
        "date": "2019-07-09",
        "time": "1:30 PM",
        "timezone": "America/Los Angeles",
        "type": "Wellness Exam",
        "reason": "Just moved to town"
      },
      "note": "Milo used to be called Olim",
    },
    {
      "pet": {
        "name": "Pumpkin",
        "species": "cat",
        "breed": "Domestic Short Hair",
        "birthdate": "2009-07-11",
        "profile_pic": "base64:jpeg,9ua-v9XASef2-="
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
      "appointment": {
        "date": "2019-07-09",
        "time": "1:30 PM",
        "timezone": "America/Los Angeles",
        "type": "Illness/injury",
        "reason": "Persistent cough"
      },
      "note": "Doesn't like being touched on the head",
    }
  ]
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
bookings | object[] | Contains information about each pet and appointment
bookings.pet | object | The pet to be seen.
bookings.pet.name | name | Pet's name.
bookings.pet.species | string | Pet's species, e.g. `cat` or `dog`.
bookings.pet.breed | string? | Pet's breed.
bookings.pet.birthdate | date? | Date portion of the pet's birthdate
bookings.pet.profile_pic | base64? | Base64 encoding of the pet's profile image
bookings.place_ids | int[]? | List of IDs from the `place` table who we will contact for the pet's medical history. One of `place_ids` or `vets` must be specified.
bookings.vets | object[]? | Vets who we will contact for the pet's medical history. These will become new `place` table entries. One of `place_ids` or `vets` must be specified.
bookings.vets.name | string | Vet name.
bookings.vets.city | string? | Vet's city.
bookings.vets.state | string? | Vet's state.
bookings.vets.zip | string? | Vet's zip code.
bookings.appointment | object? | Appointment details
bookings.appointment.date | string? | Date portion of the appointment, e.g. "2019-07-20".
bookings.appointment.time | string? | Time portion of the appointment in 24 hour time, e.g. "15:30" or "09:45".
bookings.appointment.timezone | string? | https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
bookings.appointment.type | string? | Broad category; see `appointment_type` table in database
bookings.appointment.reason | string? | More details about why this appointment is being made
bookings.note | string? | Note to the vets.
signature | string | Signature of the user's consent.

## Update intake auxiliary info (user)
> Request example

```json
{
  "pet": {
    "profile_pic": "https://s3.aws.amazon.com/pawprint-intake-pet/0003.jpg",
  },
	"appointment": {
    "date": "2019-07-20",
    "time": "15:30",
    "timezone": "America/Los Angeles",
    "type": "Injury/Illness",
    "reason": "Mass on right hind leg"
  }
}
```

> Response example

```json
(none)
```

Updates an intake form; anyone with the link/intake external ID can update this.
Fields other than the profile pic and appointment fields will be ignored.

### HTTP Request
`PUT /intake/:intake_id/aux`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
pet.profile_pic | string? | URL of the pet's profile picture.
appointment | object? | Appointment details
appointment.date | string? | Date portion of the appointment, e.g. "2019-07-20".
appointment.time | string? | Time portion of the appointment in 24 hour time, e.g. "3:30 pm" or "9:45 am".
appointment.timezone | string? | https://en.wikipedia.org/wiki/List_of_tz_database_time_zones.
