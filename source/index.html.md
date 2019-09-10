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

<h1 id='interacting-with-the-api' style='width:100%;background-color:transparent;border-top:none;background-color:#18355C;color:white;'>
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
curl -v -X POST https://kobalt001.syndication.com/api/v1/whitelisted \ 
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
      "url":"http://<URL>.syndication.com" 
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
      "url":"http://<URL>.syndication.com" 
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
      "url":"http://<URL>.syndication.com" 
    }
  ]
}

```

<aside class="warning">Rate limits are enforced to prevent over-processing of data.</aside>

Globally, rate limits are enforced on UI-based endpoints. This prevents users from excessively calling endpoints for different data and/or analyses. 

Currently, we set the rate limits at **5 calls per minute**. An error is displayed in the UI if the request is unable to be processed due to rate limits exceeded on the server. 

# Changelog

Recent changes and additions to KOBALT API v1. Changes marked with `[VERSIONED]` include a versioned change and are only available as the default version. Version cannot be specified as a parameter. Follow [here](https://<URL>.syndication.com) for versioning updates.



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
<h1 id='analysis-endpoints' style='width:100%;background-color:transparent;border-top:none;background-color:#18355C;color:white;'>
Analysis Endpoints
</h1>

<aside class="success">
Analysis endpoints are prefixed for versioning.
</aside>

### API Endpoint Prefix

`https://kobalt001.syndication.com/api/v1`

> <p style='font-weight:600;'>Sending a request to base URL prefix:</p>

```shell
curl -v -X GET https://kobalt001.syndication.com/api/v1 \ 
  -H 'Content-Type: application/json'
  -H 'Accept: application/json'
```

> <p style='font-weight:600;'>The above command responds with API status:</p>

```json
{
  "state": "success",
  "message": "The server is active and the API is live."
}
```

#### Example

`https://kobalt001.syndication.com/api/v1/pv-search`

# Short Analysis
<aside class="notice">
<em>Note: Endpoints for Developer Resources are found below under <a href="#developer-resources">Developer Resources</a>.</em>
</aside>

The `Short Analysis` is *not* meant to provide actionable data. It is meant to provide a preview of the more robust `Long Analysis` data for quick insight into how impactful IHP work may be on the respective account.

### Use Cases:

- Preliminary discovery of inactive modes;
- Analysis of individual, by-mode PVs;
- Quick reference for client insights.

## GET Active Modes

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
curl "https://kobalt001.syndication.com/api/v1/short-pv-reader?publisher=1010748&date=2019-07-01" \ 
  -H 'Content-Type: application/json'
  -H 'Accept: application/json'
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns HTML structured like this:

```html
<span>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>mode</th>
      <th>num_views</th>
      <th>publishers</th>
      <th>placements</th>
      <th>num_placements</th>
      <th>num_publishers</th>
      <th>without_abp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>organic-thumbs-feed-01:abp=0</td>
      <td>791580</td>
      <td>[walla]</td>
      <td>[Mobile Below Article Thumbnails]</td>
      <td>1</td>
      <td>1</td>
      <td>organic-thumbs-feed-01</td>
    </tr>
```

This endpoint returns a baked dataframe of currently active modes based on PVs.

### HTTP Request

`GET https://kobalt001.syndication.com/api/v1/short-pv-reader`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
<div>`publisher` <div><em style='font-size:12px;'>_required_</em></div></div> | `null` | Numerical publisher ID for account.
<div>`date` <div><em style='font-size:12px;'>_optional_</em></div></div> | `null` | If unspecified, the API assigns a date 90-days prior to the current day's date. Must be in `YYYY-mm-dd` format.

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
curl "https://kobalt001.syndication.com/api/v1/pv-reader"
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

# Long Analysis

The `Long Analysis` is meant to provide actionable data. It provides a more robust, definitive analysis of data into how impactful IHP work may be on the respective account. Without exceptions--which are noted if any exist--the `Long Analysis` provides conclusive findings regarding inactive vs. active modes.

<div style="height:50px;"></div>
<h1 id='plotting-endpoints' style='width:100%;background-color:transparent;border-top:none;background-color:#18355C;color:white;'>
Plotting Endpoints
</h1>

# Plot Analysis

<div style="height:50px;"></div>
<h1 id='developer-resources' style='width:100%;background-color:transparent;border-top:none;background-color:#18355C;color:white;'>
Developer Resources
</h1>

The API endpoints for the Developer Resources are the most modular and extensible. 

# Source Structure of Endpoints

## Basic Endpoint Structure and Requirements


```python
@app.route('/api/v1/<ENDPOINT>', methods=['GET'])
@limiter.limit("1000 per hour")
def impl_pv_reader():

    if not request or request.args.get('publisher') is None:
        abort(400, description='Publisher required')

    param = request.args.get('publisher', type=int)
    date_object=None
    base_date_object = datetime.date.today() - datetime.timedelta(3*30)

    if request and request.args.get('date') is None:
        date_object = str(base_date_object)
    else:
        date_object = request.args.get('date')
        # Condition to prevent searches > 3 months prior
        if pd.to_datetime(date_object) < pd.to_datetime(base_date_object):
            date_object = str(base_date_object)

    # Main logic 
    try:
        connection = vertica_connector()

        if connection:
            cursor = connection.cursor()
            query = pv_vertica_search(param, date_object)
            cursor.execute(query)
            json_data=data_json_formatter(cursor)
            df = pd.DataFrame(data=json_data)
            db_connection_sever(cursor, connection)
            # -*- -*- Data Cleaning / Engineering -*- -*-
            df = data_engineer(df)
            #  -*- -*- -*- -*- -*- -*- -*- -*- -*-
            response_object = {
                'state':'success',
                'message':'processed successfully',
                'data':[{
                    'json_response':df.head(25).to_dict(orient='records')
                    }]
            }
            return response_object
    except:
        abort(404, description='No data or resources not found')`
```
<aside class="success">
Click <code>python</code> to see the API endpoint definition.
</aside>
### HTTP Request

`GET https://kobalt001.syndication.com/api/v1/<ENDPOINT>`

### Availble Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
<div>`publisher` <div><em style='font-size:12px;'>_required_</em></div></div> | `null` | Numerical publisher ID for account.
<div>`date` <div><em style='font-size:12px;'>_optional_</em></div></div> | `null` | If unspecified, the API assigns a date 90-days prior to the current day's date. Must be in `YYYY-mm-dd` format.

# Baked Modes Request

## GET Loader-baked Modes

```shell
curl "https://kobalt001.syndication.com/api/v1/impl-list-modes?publisher=nbcnews" \ 
  -H 'Content-Type: application/json'
  -H 'Accept: application/json'
```

> <p style='font-weight:600;'>The above command returns JSON structured like this:</p>

```json
{
    "data": [
        {
            "json_response": [
                {
                    "mode_date": "Thu, 25 Oct 2018 13:17:47 GMT",
                    "mode_id": 370681,
                    "mode_name": "thumbs-feed-text-links-4-links"
                },
                {
                    "mode_date": "Wed, 17 Oct 2018 09:11:52 GMT",
                    "mode_id": 367886,
                    "mode_name": "thumbs-feed-stream-half-size"
                },
                {
                    "mode_date": "Sun, 14 Oct 2018 08:41:43 GMT",
                    "mode_id": 364683,
                    "mode_name": "thumbs-feed-hero-bottom"
                },
                {
                    "..."
                },
            ]
        }
    ],
    "message": "processed successfully",
    "state": "success"
}
```

### HTTP Request

`GET https://kobalt001.syndication.com/api/v1/impl-list-modes`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
<div>`publisher` <div><em style='font-size:12px;'>_required_</em></div></div> | `null` | Descriptive publisher ID for account.

# Short Summary Request

## GET Active Modes

```shell
curl "https://kobalt001.syndication.com/api/v1/impl-short-pv-reader?publisher=1010748" \ 
  -H 'Content-Type: application/json'
  -H 'Accept: application/json'
```

> <p style='font-weight:600;'>The above command returns JSON structured like this:</p>

```json
{
    "data": [
        {
            "json_response": [
                {
                    "mode": "thumbnails-feed-mobile",
                    "num_views": 472295922,
                    "publisher_id": 1010748
                },
                {
                    "mode": "thumbnails-feed-mobile-amp",
                    "num_views": 342570899,
                    "publisher_id": 1010748
                },
                {
                    "mode": "organic-thumbnails-feed-mobile",
                    "num_views": 296182519,
                    "publisher_id": 1010748
                },
                {
                    "..."
                },
            ]
        }
    ],
    "message": "processed successfully",
    "state": "success"
}
```

### HTTP Request

`GET https://kobalt001.syndication.com/api/v1/impl-short-pv-reader`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
<div>`publisher` <div><em style='font-size:12px;'>_required_</em></div></div> | `null` | Numerical publisher ID for account.
<div>`date` <div><em style='font-size:12px;'>_optional_</em></div></div> | `null` | If unspecified, the API assigns a date 90-days prior to the current day's date. Must be in `YYYY-mm-dd` format.

# Long Summary Request

## GET Initial Data Engineering

```shell
curl "https://kobalt001.syndication.com/api/v1/impl-pv-reader?publisher=1010748&date=2019-07-01" \ 
  -H 'Content-Type: application/json'
  -H 'Accept: application/json'
```

> <p style='font-weight:600;'>The above command returns JSON structured like this:</p>

```json
{
    "data": [
        {
            "json_response": [
                {
                    "mode": "organic-thumbs-feed-01:abp=0",
                    "num_placements": 1,
                    "num_publishers": 1,
                    "num_views": 791580,
                    "placements": [
                        "Mobile Below Article Thumbnails"
                    ],
                    "publishers": [
                        "walla"
                    ],
                    "without_abp": "organic-thumbs-feed-01"
                },
                {
                  "mode": "...",
                  "num_placements": "...",
                  "..."
                }
            ]
        }
    ],
    "message": "processed successfully",
    "state": "success"
}
```

### HTTP Request

`Endoint: /impl-pv-reader`

`GET https://kobalt001.syndication.com/api/v1/impl-pv-reader`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
<div>`publisher` <div><em style='font-size:12px;'>_required_</em></div></div> | `null` | Numerical publisher ID for account.
<div>`date` <div><em style='font-size:12px;'>_optional_</em></div></div> | `null` | If unspecified, the API assigns a date 90-days prior to the current day's date. Must be in `YYYY-mm-dd` format.