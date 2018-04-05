# Activation
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
`POST /user/pet/:petid/share`

### POST parameters
Parameter | Type | Description
--------- | ---- | -----------
recipients | string | Comma-separated list of recipient emails
