# Sharing
For inviting, onboarding and account linking.

## Share pet records

> Request example

```json
{
	"recipients": "demo@getpawprint.com, demo2@getpawprint.com"
}
```

> Response example

```json
(none)
```

Shares pet records via email. The email contains the pet's name, vaccinations, and links to PDFs were both
user-uploaded and admin-uploaded (from a records request).

### HTTP Request
`POST /user/pets/:petid/share`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
recipients | string | Comma-separated list of recipient emails

## Check pet invite code

> Response example

```json
{
  pet_id: 101
}
```

Checks if an invite code exists. This is when you share a pet. Returns the pet ID associated with the invite code.

### HTTP Request
`GET /invite_code/:inviteCode`

## Create pet invite code
> Request example

```json
{
	"access": 1
}
```

> Response example

```json
{
	"code": 123456,
	"access": 1,
	"expiry": "2018-06-01T12:00:00Z"
}
```

Creates a pet invite code for the purpose of sharing pet ownership with another user.
Access levels are 1 for full ownership and 2 for limited ownership; however, access levels are deprecated.

### HTTP Request
`POST /user/pets/:petid/invite_code`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
access | int | Deprecated. 1 for full ownership; 2 for limited ownership.

## Use pet invite code

> Response example

```json
{
    pet_id: 53540
}
```

Uses a pet invite code. Note that this is a GET and not a POST; this is an old API.
When the pet invite code is used, a relationship is created in the `user_pet` table between the pet
and the calling user, and all pet reminders are copied to the user's reminders in `user_reminder_v2` as well.
The proper design would specify this as a POST and v2 may change the verb in the future.

### HTTP Request
`GET /user/invite_code/:code`
