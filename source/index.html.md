---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Kittn API! You can use our API to access Kittn API endpoints, which can get information on various cats, kittens, and breeds in our database.

We have language bindings in Shell, Ruby, Python, and JavaScript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/lord/slate). Feel free to edit it and use it as a base for your own API's documentation.

<h1 style='width:100%;background-color:transparent;border-top:none;'>
Interacting with the API
</h1>

## Status Codes

* `200 OK` Successful request
* `201 Created` New object saved
* `204 No Content` Object deleted
* `400 Bad Request` Returns JSON with the error message
* `401 Unauthorized` Couldn't authenticate your request
* `404 Not Found` No such object
* `500 Internal Server Error` Something went wrong
* `503 Service Unavailable` Service is down for maintenance

## Making Requests

As per RESTful design patterns, KOBALT API implements the following HTTP verbs:

* `GET` - Read resources
* `POST` -  Create new resources
* `PUT` - Modify existing resources
* `DELETE` - Remove resources

When making requests, arguments can be passed as params, form data, or JSON with correct `Content-Type` header. 

## CORS

KOBALT API v1 supports cross-origin HTTP requests which is commonly referred as [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS). This means that you can call API resources using Javascript from any browser. CORS is mainly useful with unauthenticated endpoints and OAuth2 client side applications.

# Scopes

Both API key and OAuth2 authentication require that you obtain correct permissions (scopes) to access different API endpoints.

As a general rule, you should only ask for scopes which your application needs and avoid asking for access to unnessary ones. Users more readily grant access to limited, clearly described scopes.

# Row Limits (Pagination)

> <p style='font-weight:600;'>Basic request to retrieve list of the first 50 whitelisted accounts:</p>

```shell
# With shell, you can just pass the correct header with each request
curl -v -X POST https://kobalt001.taboolasyndication.com/api/v1/whitelisted \ 
  -H 'Content-Type: application/json'
  -H 'Accept: application/json'
  -d '
      {
          "limit": 50,
      }'
```

> <p style='font-weight:600;'>The above command returns JSON structured like this:</p>

```json
{
  "state": "success",
  "message": "processing data for request",
  "limit":50,
  "data": [{
          "data":"<DATA in request>",
          "data_length": "<LENGTH of data request>",
      }]
}
```

`dataframe` `GET` endpoints returnn an object list which supports variable limit lengths. To get all rows associated with a `dataframe`, it is necessary to pass `limit=full` as a param. 

Default `limit` is set to 25 rows, and it is recommended not to exceed 100 rows (e.g., `limit=100`). 

The result list is in ascending order by default (oldest item first) but can be reversed by supplying `order=desc` instead.

### ARGUMENTS

| <img width=100 />Parameter    | Description |
| ---:        |    :----   |
| <div>`limit` <div><em style='font-size:12px;'>_optional_</em></div></div>| Number of results per call (e.g., number of rows). Accepted values: 0 - full. Default: 25       | 
| <div>`order` <div><em style='font-size:12px;'>_optional_</em></div></div>  | Result order. Accepted values: `asc` (default), `desc`        |

# Errors

## Error Response

> <p style='font-weight:600;'>Generic error response (4xx, 5xx)</p>

```json
{
  "errors": [
    {
      "id": "no_data_error",
      "message": "No data",
      "error_description": "Please ensure account is whitelisted" 
    }
  ]
}

```

> <p style='font-weight:600;'>Validation failed (400)</p>

```json
{
  "errors": [
    {
      "id": "validation_error",
      "message": "Not authorized to access",
      "error_description": "Please make sure you are authorized to access" 
    }
  ]
}

```

> <p style='font-weight:600;'>Error with document link</p>

```json
{
  "errors": [
    {
      "id": "not_whitelisted_error",
      "message": "Account not whitelisted",
      "error_description": "Account is not whitelisted for analysis",
      "url":"http://<URL>.taboolasyndication.com" 
    }
  ]
}

```

All error messages will return both machine (`id`) and human readable (`message`) error message. All errors, except `validation_error`, return only one error. Some errors will also have an optional link to the documentation (`url`).

*Important*: Different error types (`id`) can be added and removed over time so you should make sure your application accepts new ones as well.

Error ID <div style="width:190px">| <div style="width:90px">Code</div> | Description
:--- | :--- | :---
`param_required_error` | 400 | Missing parameter
`validation_error` | 400 | Unable to validate `POST/PUT` request
`invalid_request_error` | 400 | Invalid request
`authentication_error` | 400 | Invalid auth (generic)
`no_data_error` | 404 | No data, resource not found
`not_whitelisted_error` | 404 | Specified account is not whitelisted for analysis
`rate_limit_error` | 429 | Rate limit exceeded
`internal_server_error` | 500 | Internal server error

<aside class="notice">
If any error persists and the cause is unclear, reach out to `<email>`.
</aside>

## Warnings

> <p style='font-weight:600;'>Example warning</p>

```json
{
  "warnings": [
    {
      "id": "daterange_warning",
      "message": "Daterange is excessive",
      "error_description": "Please limit the scope of your query to a few weeks",
      "url":"http://<URL>.taboolasyndication.com" 
    }
  ]
}

```

Responses can include a `warnings` parameter to notify the developer of best practices, implementation suggestions or deprecation warnings. While you don’t need show warnings to the user, they are usually something you need to act on so it’s recommended that you add them to admin email alerts.

# Rate Limits

> <p style='font-weight:600;'>Rate Limit Error (429)</p>

```json
{
  "errors": [
    {
      "id": "rate_limit_error",
      "message": "Too many requests",
      "error_description": "Please make your request again in a few minutes",
      "url":"http://<URL>.taboolasyndication.com" 
    }
  ]
}

```

<aside class="warning">Rate limits are enforced to prevent over-processing of data.</aside>

Globally, rate limits are enforced on UI-based endpoints. This prevents users from excessively calling endpoints for different data and/or analyses. 

Currently, we set the rate limits at **5 calls per minute**. An error is displayed in the UI if the request is unable to be processed due to rate limits exceeded on the server. 

# Changelog

Recent changes and additions to KOBALT API v1. Changes marked with `[VERSIONED]` include a versioned change and are only available as the default version. Version cannot be specified as a parameter. Follow [here](https://<URL>.taboolasyndication.com) for versioning updates.



# OOP Configuration

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>


<div style="height:50px;"></div>
<h1 style='width:100%;background-color:transparent;border-top:none;'>
Analysis Endpoints
</h1>

# Short Analysis

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/dataframe`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

<div style="height:50px;"></div>
<h1 style='width:100%;background-color:transparent;border-top:none;'>
Plotting Endpoints
</h1>

# Plot Analysis