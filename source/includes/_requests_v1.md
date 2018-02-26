# Requests v1
Hopefully deprecated soon

## Check if promo code is valid

> Response example

```json
{
    "valid": false,
    "msg": "The code foo has expired."
}
```

```json
{
    "valid": true,
    "id": 6,
    "msg": null
}
```

Checks if a promo code is valid.

### HTTP Request
`GET /user/request/:code`

