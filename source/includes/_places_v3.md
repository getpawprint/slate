# Places
Location-aware search. Why did Place v2 get skipped? There was already an internal place V2 in the backend, and so an internal place V3 was created. The new external-facing place API was versioned to V3 to follow suit.

## Location-aware search

> Response example

```json
[
    {
        "id": "ChIJ45p0tN8UkFQR_Di6lOVS1LE",
        "name": "Eric P. Hartmann, DVM",
        "placesid": "ChIJ45p0tN8UkFQR_Di6lOVS1LE",
        "website": "http://www.acupuncture5element.com/",
        "contactMethod": "phone",
        "address": "2026 10th Ave E, Seattle, WA 98102, USA",
        "fax": null,
        "email": null,
        "phone": "(206) 769-6079",
        "verified": false,
        "type": "vet",
        "pawprint_note": null,
        "custom_message": null,
        "geo": {
            "lat": 47.6380563,
            "lng": -122.3198314
        },
        "pawprint_vet": false,
        "pawprint_vet_id": null
    },
    {
        "id": "ChIJ2dNI-GNYkFQRVF87976znKg",
        "name": "Auburn Veterinary Hospital Inc",
        "placesid": "ChIJ2dNI-GNYkFQRVF87976znKg",
        "website": "http://www.auburnveterinaryhospital.net/",
        "contactMethod": "phone",
        "address": "718 Auburn Way N, Auburn, WA 98002, USA",
        "fax": null,
        "email": null,
        "phone": "(253) 833-4510",
        "verified": false,
        "type": "vet",
        "pawprint_note": null,
        "custom_message": null,
        "geo": {
            "lat": 47.3139545,
            "lng": -122.224466
        },
        "pawprint_vet": false,
        "pawprint_vet_id": null
    },
    {
        "id": "ChIJ2dNI-GNYkFQRykqimLQ-EX4",
        "name": "Dr. Kimo Jow, DVM",
        "placesid": "ChIJ2dNI-GNYkFQRykqimLQ-EX4",
        "website": "http://www.auburnveterinaryhospital.net/",
        "contactMethod": "phone",
        "address": "718 Auburn Way N, Auburn, WA 98002, USA",
        "fax": null,
        "email": null,
        "phone": "(253) 833-4510",
        "verified": false,
        "type": "vet",
        "pawprint_note": null,
        "custom_message": null,
        "geo": {
            "lat": 47.3139545,
            "lng": -122.224466
        },
        "pawprint_vet": false,
        "pawprint_vet_id": null
    },
    {
        "id": "ChIJmxj6ASv8j1QRi25CowW_qzQ",
        "name": "South Whidbey Animal Clinic",
        "placesid": "ChIJmxj6ASv8j1QRi25CowW_qzQ",
        "website": "http://www.southwhidbeyac.com/",
        "contactMethod": "phone",
        "address": "11197 WA-525, Clinton, WA 98236, USA",
        "fax": null,
        "email": null,
        "phone": "(360) 341-1200",
        "verified": false,
        "type": "vet",
        "pawprint_note": null,
        "custom_message": null,
        "geo": {
            "lat": 47.9917922,
            "lng": -122.3978984
        },
        "pawprint_vet": false,
        "pawprint_vet_id": null
    },
    {
        "id": "ChIJBVTlRO9UkFQRmibpVVjksj8",
        "name": "Dr. Anthony Brown",
        "placesid": "ChIJBVTlRO9UkFQRmibpVVjksj8",
        "website": "http://www.vetnorthend.com/veterinarian/wa/tacoma/veterinarians",
        "contactMethod": "phone",
        "address": "3819 N 25th St, Tacoma, WA 98406, USA",
        "fax": null,
        "email": null,
        "phone": "(253) 777-0068",
        "verified": false,
        "type": "vet",
        "pawprint_note": null,
        "custom_message": null,
        "geo": {
            "lat": 47.2704067,
            "lng": -122.4883595
        },
        "pawprint_vet": false,
        "pawprint_vet_id": null
    },
    {
        "id": 27228,
        "name": "Eric's Place",
        "placesid": null,
        "website": null,
        "contactMethod": null,
        "address": "123 Main Street, Bellevue WA 98004",
        "fax": null,
        "email": null,
        "phone": null,
        "verified": null,
        "type": null,
        "pawprint_note": null,
        "custom_message": null,
        "geo": null,
        "vet_logo": null,
        "pawprint_vet": false,
        "pawprint_vet_id": null
    }
]
```

This is a smarter search than before. This search is optionally location-aware and user-aware.

**Location awareness**: If geocoordinates are passed in the `location` parameter, the search will be limited to a 50 km (30 mi) radius around that point. If not, then a global search is performed, and the results will probably not be what the user wanted.

**User awareness**: The search can be performed anonymously, but if an auth token is part of the request, the results will also include any custom places created by that user. For example, if a user created a place named "Eric's place", and then searched for "Eric", then "Eric's place" will be part of the search results FOR THAT USER ONLY. However, this changes if an admin verifies the existence of that vet in the admin portal.

#### Nitty gritty
**Verification level**: - Each place can have one of four verification levels: `user`, `admin`, `google` and `partner`. 
Level  | Description
------ | -----------
user | Created by a user; not verified by an admin. These places are not showed to anyone except their creators.
admin | Verified by an admin via the admin portal, usually when taking care of a request.
google | The place wasn't in our database, but was added by a user and has information filled in from Google Place Details.
partner | The place came as a result of a list supplied by a partner (e.g. Banfield) or was scraped off an authoritative website.

**Merging of results**: Results come from the Pawprint database and a Google Places text search. If `location` was passed, they are ordered by distance; otherwise they are ordered by Pawprint partner vets first, then Pawprint databases, then Google Places results. For the last type, `id` may be a Google Place ID instead of an row ID from the `place` table.

### HTTP Request
`GET /v3/place`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
0 | string | The search term.
location | string? | Geocoordinates of the center of the search in _lat,lng_ format, e.g. `location=47.620135,-122.19779`

## Suggest a city

> Response example

```json
[
    {
        "city": "Aberdeen, WA, USA",
        "placesid": "ChIJxag0MH8kklQRdZl18Pnt2as"
    },
    {
        "city": "Aberdeen, MD, USA",
        "placesid": "ChIJAeMIvYXBx4kRdLSNqBgdFj4"
    }
]
```

Searches Google Places Autocomplete for cities for a given prefix, e.g. `prefix=Aberdeen`. Geocoordinates are not returned
because Google Places Autocomplete doesn't return them; when performing a location aware search, pass in the `placesid` instead
and the backend will get its coordinates automatically.

### HTTP Request
`GET /v3/place/city`

### Query string parameters
Parameter | Type | Description
--------- | ---- | -----------
prefix | string | The search prefix.