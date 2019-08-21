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

# Interacting with the API

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
curl -v -X POST https://kobalt001.taboolasyndication.com/v1/whitelisted \ 
  -H 'Content-Type: application/json'
  -H 'Accept: application/json'
  -d '
      {
          "limit": 50,
      }'
```

> <p style='font-weight:600;'>The above command returns JSON structured like this:</p>

```json
[
  {
    "state": "success",
    "message": "processing data for request",
    "limit":50,
    "data": [{
            "data":"<DATA in request>",
            "data_length": "<LENGTH of data request>",
        }]
  }
]
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

# Simple Calls

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

# Kittens

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

