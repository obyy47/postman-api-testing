# Postman API Testing

API tests built in Postman for two public practice APIs. Each request has assertions, and requests are chained with environment variables (for example an auth token or a booking ID that is saved by one request and used by the next).

| API | Base URL | Requests | Tests (assertions) |
|---|---|---|---|
| [Reqres](https://reqres.in) | `https://reqres.in` | 12 | 29 |
| [Restful Booker](https://restful-booker.herokuapp.com) | `https://restful-booker.herokuapp.com` | 9 | 21 |

## What is checked

- **Status codes:** 200, 201, 204, 400, 403, and 404
- **Response body:** field values returned by the server
- **JSON Schema:** types and required fields of the response
- **Response time:** a delayed endpoint must respond in under 5 seconds
- **Negative cases:** user not found, missing password on register and login, update without a token, and a booking that does not exist
- **Request chaining:** values such as `token`, `booking_id`, `user_id`, and `reqres_token` are saved to the environment and reused

## Reqres API: requests

| # | Request | What is checked |
|---|---|---|
| 01 | GET `/api/users?page=2` | 200, page number, data array, JSON schema |
| 02 | POST `/api/users` | 201, returned name and job, ID present, JSON schema; saves `user_id` |
| 03 | PUT `/api/users/{{user_id}}` | 200, job updated, `updatedAt` present, JSON schema |
| 04 | GET `/api/users/2` | 200, user details, JSON schema |
| 05 | GET `/api/users/23` | **Negative:** 404 and empty body |
| 06 | POST `/api/register` | **Negative:** 400 and "Missing password" |
| 07 | POST `/api/login` | 200, token returned, JSON schema; saves `reqres_token` |
| 08 | POST `/api/login` | **Negative:** 400 and "Missing password" |
| 09 | DELETE `/api/users/{{user_id}}` | 204 |
| 10 | GET `/api/users?delay=3` | 200 and response time under 5000 ms |
| 11 | GET `/api/unknown` | 200, resource list, color field, JSON schema |
| 12 | GET `/api/unknown/2` | 200 and resource details |

## Restful Booker API: requests

| # | Request | What is checked |
|---|---|---|
| 01 | POST `/auth` | 200, token returned and saved, JSON schema |
| 02 | POST `/booking` | 200, booking ID returned and saved, JSON schema |
| 03 | GET `/booking/{{booking_id}}` | 200, names match the input, JSON schema |
| 04 | PUT `/booking/{{booking_id}}` | 200, updated price and notes, JSON schema (uses the token) |
| 05 | PATCH `/booking/{{booking_id}}` | 200, only the sent fields changed, JSON schema |
| 06 | GET `/booking?firstname=...&lastname=...` | 200, non-empty array of booking IDs, JSON schema |
| 07 | PUT `/booking/{{booking_id}}` without token | **Negative:** 403 Forbidden |
| 08 | GET `/booking/99999999` | **Negative:** 404 Not Found |
| 09 | DELETE `/booking/{{booking_id}}` | 201 |

The requests depend on each other, so run them **in this order**. Delete runs last because the other requests need the booking to still exist.

## How to run

1. Import the collection from [`collections/`](./collections) and the matching environment from [`environments/`](./environments) into Postman.
2. Select the environment. `base_url` is already filled in.
3. For Reqres, set `api_key`: the collection sends it as an `x-api-key` header on every request (collection-level API Key authorization). If the API asks for a key, create one on the Reqres website and paste it into the **current value** of `api_key`. Do not commit your key.
4. Open the collection, click **Run**, and run all requests in order.

The credentials used in the requests (the Restful Booker `admin` account and the Reqres demo login) are the public demo credentials from each API's documentation.

## Results

Collection Runner results are in [`screenshots/`](./screenshots):

- `reqres-runner.png`
- `restful-booker-runner.png`

## Repository structure

```
postman-api-testing/
├── README.md
├── collections/
│   ├── reqres-api-tests.json
│   └── restful-booker-api-tests.json
├── environments/
│   ├── reqres.postman_environment.json
│   └── restful-booker.postman_environment.json
└── screenshots/
```

## Next

- Run the collections from the command line with Newman
- Add more negative cases (invalid token, invalid input values)
