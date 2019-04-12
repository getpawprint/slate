# Intake forms
Intake forms are for vets who are taking on new clients. The vet enters basic information about the new client,
such as name, email address and pet name into a form, then submits the form. An external ID is created for the partially
completed form, and then a link is emailed to the new client. When the client clicks the link, they will see another form with
the partially complete information that the vet entered. They should then enter the rest of the information about the pet,
such as species, date of birth, and most importantly, the other vets in the pet's medical history. 
Once they have completed the form, they can submit it, which causes record requests to be created in the Pawprint system.
When updated or completed, the records are emailed to both the client and their new vet.

## Create intake form (vet)
> Request example

```json
{
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555"
  },
  "pet": {
    "name": "Milo"
  },
	"appointment": {
    "date": "2019-07-20",
    "time": "15:30"
  }
}
```

> Response example

```json
(none)
```

Initializes an intake form with the given information. The example shows all available fields,
but only a small subset is necessary for creation. An external ID for the form is generated and emailed to the new client,
which can be used to fill in the rest of the fields.

**TODO: how does authentication & partner selection work?**

### HTTP Request
`POST /intake`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
partner_id | int | **Temporary until auth is finalized** ID from the partner table
user | object | The new client.
user.first_name | string | New client's first name.
user.last_name | string | New client's last name.
user.email | string | New client's email address.
user.phone | string? | New client's phone number.
pet | object | The pet to be seen.
pet.name | name | Pet's name.
pet.species | string? | Pet's species, e.g. `cat` or `dog`.
pet.breed | string? | Pet's breed.
pet.birthdate | date? | Date portion of the pet's birthdate
appointment | object | Appointment details
appointment.date | string | Date portion of the appointment, e.g. "2019-07-20".
appointment.time | string? | Time portion of the appointment in 24 hour time, e.g. "15:30" or "09:45".
appointment.timezone | string? | https://en.wikipedia.org/wiki/List_of_tz_database_time_zones


## Get intake form

> Response example

```json
{
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555"
  },
  "pet": {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix",
    "birthdate": "2018-02-16"
  },
	"appointment": {
    "date": "2019-07-20",
    "time": "15:30",
    "timezone": "America/Los Angeles"
  }
}
```

Gets the specified intake form. Not all fields are guaranteed to be populated.

### HTTP Request
`GET /intake/:intake_id`

## Update intake form (user or vet)
> Request example

```json
{
  "user": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "johnsmith@getpawprint.com",
    "phone": "555-555-5555"
  },
  "pet": {
    "name": "Milo",
    "species": "dog",
    "breed": "Australian Cattle Dog Mix",
    "birthdate": "2018-02-16"
  },
  "place_ids": [ ],
  "vets": [
    {
      "name": "Pawprint Seattle",
      "city": "Seattle",
      "state": "WA",
      "zip": "98105"
    }
  ]
}
```

> Response example

```json
(none)
```

Updates an intake form with the given information. If one of the required fields is empty, HTTP 400/Bad Request is returned. One of `place_ids` or `vets` must be specified; both are accepted. Objects in the `vets` list will be created as new entries in the `place` table. If all of the required fields are populated, HTTP 200/OK is returned, and the record requests are created automatically.

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
