# Schenectady's Open 311 API

Schenectady's 311 system gives residents a central phone number and location to request city services, report non-emergency issues, and get updates on city work. Open311 provides greater access and transparency since it allows developers to build apps and websites on top of Schenectady's 311 system. Open311 is a REST-based Application Programmer Interface (API) that lets developers requests directly to 311 and also retrieve status on existing service requests.

This was auto-generated from the swagger / opendata specification in this repository.

**Version:** 2.0

**License:** [CC BY 3.0](http://creativecommons.org/licenses/by/3.0/)

### /services
---
##### ***GET***
**Summary:** Provide a list of service request types and associated codes

**Description:** List acceptable service request types and their associated service codes. These request types can be unique to the city/jurisdiction.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| jurisdiction_id | query | optional, set to city jurisdiction | No | string |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | ok | [ [Service](#service) ] |
| 400 | The URL request is invalid or service is not running or reachable.  Client should notify us after checking URL |  |
| 404 | jurisdiction_id provided was not found |  |

### /services/{service_code}
---
##### ***GET***
**Summary:** Extended definition of a service type

**Description:** Define attributes associated with a service code. These attributes can be unique to the city/jurisdiction.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| jurisdiction_id | query | optional, set to city jurisdiction | No | string |
| service_code | path |  | Yes | string (uuid) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | ServiceDefinition for the Service | [ServiceDefinition](#servicedefinition) |
| 400 | service_code or jurisdiction_id provided were not found |  |
| 404 | service_code or jurisdiction_id provided were not found |  |

### /requests
---
##### ***GET***
**Summary:** Query the current status of multiple requests

**Description:** Query the current status of multiple requests. Because the endpoint may return service requests with no token and no service_reqeust_id in calls to GET service_requests while recently submitted SRs are still being processed by city systems, users of the GET service_requests method should ignore any service requests returned by the API until they have a service_request_id.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| jurisdiction_id | query | optional, set to city jurisdiction | No | string |
| service_request_id | query | To call multiple Service Requests at once, multiple service_request_id can be declared; comma delimited.This overrides all other arguments. | No | string (uid) |
| service_code | query | Specify the service type by calling the unique ID of the service_code. | No | string |
| start_date | query | Earliest datetime to include in search. When provided with end_date, allows one to search for requests which have a requested_datetime that matches a given range, but may not span more than 90 days. | No | dateTime |
| end_date | query | Latest datetime to include in search. When provided with start_date, allows one to search for requests which have a requested_datetime that matches a given range, but may not span more than 90 days. | No | dateTime |
| status | query | Allows one to search for requests which have a specific status. This defaults to all statuses; can be declared multiple times, comma delimited. | No | string |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | List of matching requests (Default query limit is a span of 90 days or first 1000 requests returned, whichever is smallest). | [ [Request](#request) ] |
| 400 | jurisdiction_id was not provided or general service error (Any failure during service query processing.  Client will have to notify us) |  |
| 401 | jurisdiction_id not found |  |

##### ***POST***
**Summary:** Create a new service request

**Description:** Submit a new request for with specific details of a single service. Must provide a location via lat/long or address_string or address_id.  Once a service request has been processed by the City (this can take an undetermined amount of time), you should be able to call the GET service_request_id method (tokens/:token_id) to get back the service_request_id for a SR. Therefore, it is nesseary to poll the GET service_request_id method until an SR id is returned. Because the Chicago endpoint may return service requests with no token and no service_reqeust_id in calls to GET service_requests while recently submitted SRs are still being processed by city systems, users of the GET service_requests method should ignore any service requests returned by the API until they have a service_request_id.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| jurisdiction_id | query | optional, set to city jurisdiction | No | string |
| service_code | query | This is obtained from GET Service List method | Yes | string (uid) |
| lat | query | WGS-84 latitude | No | double |
| long | query | WGS-84 longitude | No | double |
| address_string | query |  | No | string |
| address_id | query |  | No | string (uid) |
| attribute | query | array of key/value responses based on Service Definitions. | No | [ string ] |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | success returns an tracking-id | [ [RequestResponse](#requestresponse) ] |

### /request/{service_request_id}
---
##### ***GET***
**Summary:** Retrieve individual request

**Description:** Query the current status of an individual request

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| jurisdiction_id | query | optional, set to city jurisdiction | No | string |
| service_request_id | path | Is specified in the main URL path rather than an added query string parameter. | Yes | string (uuid) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | success returns request details | [Request](#request) |

### /tokens/{token_id}
---
##### ***GET***
**Summary:** Id of service_request via token

**Description:** Get the service_request_id from a temporary token. This is unnecessary if the response from creating a service request does not contain a token.

**Parameters**

| Name | Located in | Description | Required | Schema |
| ---- | ---------- | ----------- | -------- | ---- |
| jurisdiction_id | query | optional, set to city jurisdiction | No | string |
| token_id | path |  | Yes | string (uuid) |

**Responses**

| Code | Description | Schema |
| ---- | ----------- | ------ |
| 200 | returns tuple request ID, token | [TokenResponse](#tokenresponse) |

### Models
---

### Service  

A single service (type) offered via Open311

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| service_code | string (uid) | unique identifier for the service request type | Yes |
| service_name | string | human readable name of the service request type | Yes |
| description | string | brief description of the service request type | No |
| metadata | boolean | determines whether there are additional form fields for this service type | No |
| type | string | Possible values realtime, batch, blackbox | Yes |
| keywords | [ string ] | list of keywords or tags seperated by comma | No |
| group | string | Category or group to cluster different request types e.g. “sanitation” | No |

### ServiceDefinition  

Attributes associated with a service code. These attributes can be unique to the city/jurisdiction.

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| service_code | string (uuid) | unique identifier for the service request type | Yes |
| attributes | [ [ServiceAttribute](#serviceattribute) ] |  | Yes |

### ServiceAttribute  

A single attribute extension for a service

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| variable | boolean | If only used to present information to the user within the description field or if that user input is needed | Yes |
| code | string (uid) | Unique identifier for the attribute | Yes |
| datatype | string | Type of field used for user input. According widgets will be picked for user inputs | Yes |
| required | boolean | If attributes value required to submit service request | Yes |
| datatype_description | string | Description of the datatype which helps the user provide their input | No |
| order | integer (uint) | Sort order that the attributes will be presented to the user. 1 is shown first in the list. | No |
| description | string | Description of the attribute field with instructions for the user to find and identify the requested information | No |
| values | [ [AttributeValue](#attributevalue) ] |  | No |

### AttributeValue  

Possible value for ServiceAttribute that defines lists

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| key | string (uid) | Unique identifier associated with an option for singlevaluelist or multivaluelist (analogous to the value attribute in an html option tag). | Yes |
| name | string | Human readable title of an option for singlevaluelist or multivaluelist (analogous to the innerhtml text node of an html option tag). | Yes |

### Request  

Issues that have been reported as service requests. Location is submitted via lat/long or address or address_id

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| service_request_id | string (uid) | Unique ID of the service request created. | Yes |
| status | string | Current status of the service request. | Yes |
| status_notes | string | Explanation of why status was changed to current state or more details on current status than conveyed with status alone. | No |
| service_name | string | The human readable name of the service request type. | No |
| service_code | string (uid) | The unique identifier for the service request type | Yes |
| description | string | A full description of the request or report submitted. | No |
| agency_responsible | string | Agency responsible for fulfilling or otherwise addressing the service request. | No |
| service_notice | string | Information about the action expected to fulfill the request or otherwise address the information reported. | No |
| requested_datetime | dateTime | Date and time when the service request was made. | Yes |
| updated_datetime | dateTime | Date and time when the service request was last modified. For requests with status=closed, this will be the date the request was closed. | No |
| expected_datetime | dateTime | The date and time when the service request can be expected to be fulfilled. This may be based on a service-specific service level agreement. | No |
| address | string | Human readable address or description of location. This should be provided from most specific to most general geographic unit, eg. address number or cross streets, street name, neighborhoo /district, city/town/village, county, postal code. | No |
| address_id | string | Internal address ID used by a jurisdictions master address repository or other addressing system. | No |
| zipcode | string | Postal code for the location of the service request. (Redundant and field might be removed as it should be part of adress string) | No |
| lat | double | latitude using the (WGS84) projection. | No |
| long | double | longitude using the (WGS84) projection. | No |
| media_url | string (uri) | URL to media associated with the request, eg an image. A convention for parsing media from this URL has yet to be established, so currently it will be done on a case by case basis much like Twitter.com does. For example, if a jurisdiction accepts photos submitted via Twitpic.com, then clients can parse the page at the Twitpic URL for the image given the conventions of Twitpic.com. This could also be a URL to a media RSS feed where the clients can parse for media in a more structured way. | No |

### RequestResponse  

Details for tracking an created service request

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| service_request_id | string (uuid) | Unique ID of the service request created. | No |
| token | string (uuid) | Use this to call GET service_request_id from a token. | No |
| service_notice | string | Information about the action expected to fulfill the request or otherwise address the information reported. | No |
| account_id | string (uuid) | Unique ID for the user account of the person submitting the request. | No |

### TokenResponse  

Resolves temporary token to ID of the request

| Name | Type | Description | Required |
| ---- | ---- | ----------- | -------- |
| service_request_id | string (uuid) | Unique ID of the service request created. | No |
| token | string (uuid) | Use this to call GET service_request_id | No |
