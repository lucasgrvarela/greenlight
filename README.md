Greenlight — a JSON API for retrieving and managing information about movies - a bit like the [Open Movie Database API](https://www.omdbapi.com/).

Based on the awesome [Let's Go Further book from Alex Edwards](https://lets-go-further.alexedwards.net/)


### API Example

#### Authentication
```
$ BODY='{"email": "alice@example.com", "password": "pa55word"}'

$ curl -i -d "$BODY" localhost:4000/v1/tokens/authentication
HTTP/1.1 201 Created
Content-Type: application/json
Vary: Origin
Vary: Access-Control-Request-Method
Vary: Authorization
Date: Tue, 20 Feb 2024 11:05:03 GMT
Content-Length: 125

{
        "authentication_token": {
                "token": "5XQIV2TIIDC5GCKF2XD5WDFW6I",
                "expiry": "2024-02-21T08:05:03.818412946-03:00"
        }
}
```

#### Get movie
```
$ curl -H "Authorization: Bearer 5XQIV2TIIDC5GCKF2XD5WDFW6I" localhost:4000/v1/movies/2
{
        "movie": {
                "id": 2,
                "title": "Black Panther",
                "year": 2018,
                "runtime": "134 mins",
                "genres": [
                        "sci-fi",
                        "action",
                        "adventure"
                ],
                "version": 3
        }
}
```

#### Permission: user with read-only can't delete
```
$ curl -X DELETE -H "Authorization: Bearer 5XQIV2TIIDC5GCKF2XD5WDFW6I" local
host:4000/v1/movies/2
{
        "error": "your user account doesn't have the necessary permissions to access this resource"
}
```

#### List movies with Pagination
```
$ curl -H "Authorization: Bearer 5XQIV2TIIDC5GCKF2XD5WDFW6I" "localhost:4000/v1/movies?page=1&page_size=1"
{
        "metadata": {
                "current_page": 1,
                "page_size": 1,
                "first_page": 1,
                "last_page": 2,
                "total_records": 2
        },
        "movies": [
                {
                        "id": 2,
                        "title": "Black Panther",
                        "year": 2018,
                        "runtime": "134 mins",
                        "genres": [
                                "sci-fi",
                                "action",
                                "adventure"
                        ],
                        "version": 3
                }
        ]
}
```

#### Middleware for Rate limit by IP
```
$ for i in {1..6}; do curl  http://localhost:4000/v1/healthcheck; done
{
        "status": "available",
        "system_info": {
                "environment": "development",
                "version": "1.0.0"
        }
}
{
        "status": "available",
        "system_info": {
                "environment": "development",
                "version": "1.0.0"
        }
}
{
        "error": "rate limit exceeded"
}
{
        "error": "rate limit exceeded"
}
```

#### Users receive an email for confirmation/activation of the account
![email with token for user activation](email.png)

#### Metrics endpoint with custom metrics like database stats and request count
![metrics](metrics.png)