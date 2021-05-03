# Preface
Welcome to the Business Objects API Guide! You can use the Business Objects API to create all the calls you need to integrate with Business Objects. This guide provides information on available endpoints and how to interact with it.

Version: `1.0`

Last updated: `2021-01-19`

## Revision History

| Date         | Version   | Description                            |
| ------------ | --------- | -------------------------------------- |
| `2021-01-19` | `1.0`     | Initial Revision (Friendly-User-Phase) |

# Introduction
The Business Objects API can be accessed over HTTPS and is based on RESTful design principles and OData V4. We use predictable resource-oriented URLs, accept JSON request bodies and return JSON responses. The only exception to this is our metadata endpoint, which is currently still based on XML. The API uses standard HTTP response codes, authentication, and verbs.

Above all, the self-describing interface of Business Objects will help you to interact with the service without having to know details about the underlying protocol.

## Prerequisites
Before you can use the Business Objects API within your application, you first have to subscribe and configure the app in your tenant. To register a new tenant or to subscribe the app visit the d.velop cloud center at https://my.d-velop.cloud/.

Since all requests to the API must be authenticated, users or API keys must also be configured that have the appropriate authorization.

## API Root endpoint
In this documentation, we will use the term *Root endpoint* to refer to the base address of the Business Objects service.

| Root endpoint | `https://<tenantDomain>.d-velop.cloud/businessobjects/` |
|---------------|---------------------------------------------------------|

Unless stated otherwise, all addresses in the rest of this documentation are relative to the root endpoint.

## Making requests
### HTTP verbs
As per RESTful design patterns, Business Objects API uses the following HTTP verbs that most clients are familiar with:

* `GET` - Read resources
* `POST` - Create new resources
* `PUT` - Replace existing resources
* `PATCH` - Update parts of resources
* `DELETE` - Remove resources

`PATCH` is recommended as the preferred means of updating an entity.

### Request Format
For `POST`, `PUT`, and `PATCH` requests, the request body is OData flavored JSON (the `Content-Type` header must be set to `application/json`).

### Response Format
The default response format is OData flavored JSON (except where noted), encoded in the UTF-8 character encoding. 

# Security
Requests must be made over HTTPS. Any non-secure requests are met with a redirect to the HTTPS equivalent URI. Make sure that your application validates certificates.

## Authentication
Only authenticated users are allowed to make API requests. So using the service requires a valid authentication of the Identity Provider App.

See Identity Provider App documentation for a detailed overview about the authentication and authorization process, on how to get such authentication and how to implement it in your application.

The `AuthSessionId` must be given to every request. This can be done in two ways:

* using the `Authorization` header with the `Bearer` scheme and a bearer token
* or via a cookie `AuthSessionID` that contains the session id

Which variant should be used depends on your requirements. If Business Objects is accessed from a browser, the use of cookies is more advisable. For server-to-server communication, a bearer token is usually the preferred way.

### Bearer
An authorization header containing a valid bearer token can be included in a request like follows:

```http
Authorization: Bearer <bearerToken>
```

You must replace <bearer_token> with your personal bearer token. It is base64 encoded and usually looks something like `cF9/EIVw...`.

> warning: The bearer token is your key to accessing all of your resources. Always keep it secure. 

### Cookie
Cookies are handled automatically in the browser. If you still have to create and transfer the cookie manually, make sure that the `AuthSessionId` has to be URL-encoded.

## Altering the state of the server
In the case of HTTP methods that change the server-side state (`POST`, `PUT`, `PATCH`, and `DELETE`), the Origin header must be transferred and must match the target origin. Otherwise the request is rejected with the status code `403 Forbidden`. The Origin request header indicates where a fetch originates from and must be set as follows:

```http
Origin: <baseURI> 
```

Set the `baseURI` variable to a valid value for your tenant. The format for this is: `https://<tenantDomain>.d-velop.cloud` (the header does not contain any path information).

## Application Roles
The following roles are used by Business Objects:

| Role | Permissions | Description |
|------|-------------|-------------|
| Tenant Admin | All application permissions | Predefined administration role with tenant wide permissions |
| Model Admin | Read and write (create, update, read) custom models and permission configuration | Administration of the model configuration and the permission configuration |
| Data Writer | Write instance data (create, update, delete) | Usually, for technical users who need write access to the instance data |
| Data Reader | Read instance data | Typically assigned to technical users who need read access to the instance data |

# Errors
If an error occurs, the response contains an HTTP status code and usually also a response body with an error code and a message. The error codes are machine-readable codes that allow clients to better understand the context of the error and respond appropriately. The message includes a user-friendly explanation of the error.

Business Objects uses conventional HTTP status codes that most clients are familiar with to indicate the success or failure of an request:

* `2xx` - Indicate success
* `4xx` - Indicate failure, the cause of the failure resulted from the provided information and typically lies in responsibility of the client
* `5xx` - Indicate errors that occurred on our servers

## Frequently used HTTP status codes
The following is a list of common HTTP status codes you see in response from the API and what they mean. Details on error handling for the individual resources can be found in the respective section.

| HTTP Status Code | Description |
|------------------|-------------|
| `200 OK` | Everything worked as expected and the response body contains the representation requested. |
| `201 Created` | The request was successful, a new resource was created and the response body contains an unexpanded representation of the created resource. The location of the newly created resource can be found in the `Location` header. |
| `204 No Content` | Everything worked as expected and the response body is empty. |
| `400 Bad Request` | The request contains syntactic or semantic errors. The response body contains further error details that the client can use to troubleshoot the problem. |
| `401 Unauthorized` | The request couldn't be authenticated. |
| `403 Forbidden` | The client doesn't have permissions to perform the request. |
| `404 Not Found` | The requested resource could not be found. |
| `409 Conflict` | The requested operation on the resource cannot be performed due to the resource status. |
| `413 Request Entity Too Large` | The request body is larger than the defined limits. |
| `414 URI Too Long` | The URI requested is longer than the defined limits. |
| `415 Unsupported Media Type` | The request was rejected because the payload format is not supported. |
| `429 Too Many Requests` | Too many requests have been sent in a given amount of time. |
| `431 Request Header Fields Too Large` | The request's HTTP headers are too long. |
| `500 Internal Server Error` | There was a technical problem with the API server. | 
| `501 Not Implemented` | The functionality required to fulfill the request is not supported. | 
| `503 Service Unavailable` | The connection is being throttled or the service is temporarily offline for maintenance. |

## Error Schema
### errorResponse
Represents an error response object.

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| error | [errorContainer](#errorcontainer) | false | The actual error payload. |

### errorContainer
Represents an error container encompassing the error payload.

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| code | string | false | A service-defined error code. |
| message | string | false | A human-readable representation of the error. |
| details | [errorDetail[]](#errordetail) | true | A collection of error details. |
| innerError | [innerError](#innererror) | true | Debugging information to help determine the error cause. |

### errorDetail
Represents an error detail.

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| code | string | false | An error detail code defined by the service. |
| message | string | false | A human-readable representation of the error detail. |

### innerError
Represents an error object with debugging information to help determine the error cause.

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| timestamp | string | true | The timestamp as the error occurred. |
| requestId | string | true | A randomly generated identifier that uniquely distinguishes each request and that can be used for correlation purposes. |


## Error codes
| Code | Description |
| ---- | ----------- |
| contentTypeNotSupported | The given content type does not match a supported MIME type. |
| divisionByZero | A division by zero error occurred while executing the query. |
| entityAlreadyExists | The entity already exists. |
| entityNotFound | The requested entity could not be found. |
| entityUnknown | One or more of the specified entity keys are unknown. |
| featureForbidden | The specified feature is forbidden. The error message contains information about the forbidden feature. |
| featureNotImplemented | The specified feature is not implemented. The error message describes the functionality not implemented. |
| limitExceeded | A limit has been exceeded. |
| moduloByZero | A modulo by zero error occurred while executing the query. |
| notDeletable | The specified element is not deletable. |
| notInsertable | The specified element is not insertable. |
| notUpdatable | The specified element is not updatable. |
| queryMalformed | The query is either syntactically or semantically malformed. |
| requestEntityMalformed | The request body is either syntactically or semantically malformed. |
| resourceNotFound | The requested resource could not be found. |
| transitionInvalid | The specified transition is invalid. |
| writeConflict | There was a conflict while attempting to write the entity. |


# Core Resources

## Models
### customModels
#### List all custom models
```http
GET core/models/customModels
```
Retrieves all custom models.

##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "value": [{
    "id": "a2ba1abe-b708-43fe-bd59-c54c4940000f",
    "name": "example1",
    "description": "First example for the API documentation",
    "state": "staged"
  },{
    "id": "83e56be3-e1dd-4641-babc-54a8e358a928",
    "name": "example2",
    "description": "Second example for the API documentation",
    "state": "published"
  }]
}
```
#### Create a new custom model
```http
POST core/models/customModels
```
Creates a new custom model.

##### Request Body
```json
{
  "name": "example",
  "description": "A sample service for Business Objects",
  "state": "initial"
}
```
##### Success Response
A successful response will have a `201 Created` HTTP status code and, for example, the following response body:

```json
{
  "id": "5d9aa2ef-397c-4bf1-b6ec-32e60bc3653e",
  "name": "example",
  "description": "A sample service for Business Objects",
  "state": "initial"
}
```
#### Get custom model details
```http
GET core/models/customModels({cmId})
```
Retrieves the information of the given custom model.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "id": "5d9aa2ef-397c-4bf1-b6ec-32e60bc3653e",
  "name": "example",
  "description": "A sample service for Business Objects",
  "state": "initial"
}
```
#### Partially update a custom model
```http
PATCH core/models/customModels({cmId})
```
Updates an existing custom model partially.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
##### Request Body
```json
{
  "description": "Partially updating a custom model for the API documentation"
}
```
##### Success Response
A successful response will have a `204 No Content` HTTP status code and will have no response body.

#### Replace a custom model
```http
PUT core/models/customModels({cmId})
```
Replaces an existing custom model.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
##### Request Body
```json
{
  "name": "api_documentation_replaced",
  "description": "Replacing a custom model for the API documentation"
}
```
##### Success Response
A successful response will have a `204 No Content` HTTP status code and will have no response body.

#### Delete a custom model
```http
DELETE core/models/customModels({cmId})
```
Delete an existing custom model.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "id": "5d9aa2ef-397c-4bf1-b6ec-32e60bc3653e",
  "name": "example",
  "description": "A sample service for Business Objects",
  "state": "initial"
}
```
### entityTypes
#### List all entity types
```http
GET core/models/customModels({cmId})/entityTypes
```
Retrieves all entity types.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "value": [{
    "id": "c30c860b-ce22-4374-9796-cec16b6fedc6",
    "name": "invoice",
    "pluralName": "invoices",
    "key": {
      "id": "67593121-e653-4875-a83d-f7aa5b4074fc",
      "name": "id",
      "type": "guid"
    }
  },{
    "id": "ed18e695-8ae4-4203-b0fd-529d1e25b5af",
    "name": "product",
    "pluralName": "products",
    "key": {
      "id": "fa49704f-9c09-47c6-8dcc-f4b2985317e0",
      "name": "id",
      "type": "guid"
    }
  }]
}
```
#### Create a new entity type
```http
POST core/models/customModels({cmId})/entityTypes
```
Creates a new entity type.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
##### Request Body
```json
{
  "name": "customer",
  "pluralName": "customers",
  "key": {
    "name": "id",
    "type": "guid",
    "description": "Unique identifier of the customer"
  },
  "description": "A simple customer schema as an example for the API documentation"
}
```
##### Success Response
A successful response will have a `201 Created` HTTP status code and, for example, the following response body:

```json
{
  "name": "customer",
  "pluralName": "customers",
  "description": "A simple customer schema as an example for the API documentation"
}
```
#### Get entity type details
```http
GET core/models/customModels({cmId})/entityTypes({etId})
```
Retrieves the information of the given entity type.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "id": "8de50cbc-3693-4406-ad1b-feda454230e2",
  "name": "customer",
  "pluralName": "customers",
  "description": "A simple customer schema as an example for the API documentation"
}
```
#### Partially update a entity type
```http
PATCH core/models/customModels({cmId})/entityTypes({etId})
```
Updates an existing entity type partially.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
##### Request Body
```json
{
  "description": "Partially updating an entity type for the API documentation"
}
```
##### Success Response
A successful response will have a `204 No Content` HTTP status code and will have no response body.

#### Replace a entity type
```http
PUT core/models/customModels({cmId})/entityTypes({etId})
```
Replaces an existing entity type.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
##### Request Body
```json
{
  "name": "TestCustomer",
  "pluralName": "TestCustomers",
  "description": "Replacing an entity type for the API documentation"
}
```
##### Success Response
A successful response will have a `204 No Content` HTTP status code and will have no response body.

#### Delete a entity type
```http
DELETE core/models/customModels({cmId})/entityTypes({etId})
```
Delete an existing entity type.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "name": "customer",
  "pluralName": "customers",
  "description": "A simple customer schema as an example for the API documentation"
}
```
### properties
#### List all properties
```http
GET core/models/customModels({cmId})/entityTypes({etId})/properties
```
Retrieves all properties.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "value": [{
    "id": "1ce177c2-ae1f-491a-9d32-0bd865d69bca",
    "name": "rating",
    "type": "decimal"
  },{
    "id": "9a4e688a-6cdf-4fed-ae82-9ecf54f410a3",
    "name": "code",
    "type": "int16"
  }]
}
```
#### Create a new property
```http
POST core/models/customModels({cmId})/entityTypes({etId})/properties
```
Creates a new property.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
##### Request Body
```json
{
  "name": "name",
  "type": "string",
  "description": "Name of the customer"
}
```
##### Success Response
A successful response will have a `201 Created` HTTP status code and, for example, the following response body:

```json
{
  "id": "20936473-f703-45b0-9305-be5066c94f15",
  "name": "name",
  "type": "string",
  "description": "Name of the customer"
}
```
#### Get property details
```http
GET core/models/customModels({cmId})/entityTypes({etId})/properties({pId})
```
Retrieves the information of the given property.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
| pId | Path | string | true | The unique identifier of the property. |
##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "id": "20936473-f703-45b0-9305-be5066c94f15",
  "name": "name",
  "type": "string",
  "description": "Name of the customer"
}
```
#### Partially update a property
```http
PATCH core/models/customModels({cmId})/entityTypes({etId})/properties({pId})
```
Updates an existing property partially.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
| pId | Path | string | true | The unique identifier of the property. |
##### Request Body
```json
{
  "description": "Partially updating a property type for the API documentation"
}
```
##### Success Response
A successful response will have a `204 No Content` HTTP status code and will have no response body.

#### Replace a property
```http
PUT core/models/customModels({cmId})/entityTypes({etId})/properties({pId})
```
Replaces an existing property.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
| pId | Path | string | true | The unique identifier of the property. |
##### Request Body
```json
{
  "name": "TestName",
  "type": "string",
  "description": "Replacing a property for the API documentation"
}
```
##### Success Response
A successful response will have a `204 No Content` HTTP status code and will have no response body.

#### Delete a property
```http
DELETE core/models/customModels({cmId})/entityTypes({etId})/properties({pId})
```
Delete an existing property.

##### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cmId | Path | string | true | The unique identifier of the custom model. |
| etId | Path | string | true | The unique identifier of the entity type. |
| pId | Path | string | true | The unique identifier of the property. |
##### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "id": "20936473-f703-45b0-9305-be5066c94f15",
  "name": "name",
  "type": "string",
  "description": "Name of the customer"
}
```
### Models Schema
#### customModel
A user-defined data model, that arranges data elements and covers the relationship of these elements to one another and to entities of reality.

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| id | string (uuid) | false | The unique identifier of the custom model (generated by the service). |
| name | string | false | The name of the custom model. It serves as part of the service root URL under which the entities are available and must be unique. |
| state | [stateType](#statetype) | true | The current state of this custom model (default: initial). |
| description | string | true | An arbitrary string attached to the custom model. Often useful for displaying it to users. |
| entityTypes | [entityType[]](#entitytype) | false | The entity types of the custom model. |

#### entityType
An entity is not determined by properties, but by an identity. For example, a person is usually represented as an entity.

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| id | string (uuid) | false | The unique identifier of the entity type (generated by the service). |
| name | string | false | The name of the entity type. It must be unique within the custom model. |
| pluralName | string | false | The plural name of the entity type. It serves as the entity set (resource) part of the URL under which the entities are available and must be unique within the custom model. |
| description | string | true | An arbitrary string attached to the entity type. Often useful for displaying it to users. |
| state | [stateType](#statetype) | true | The current state of this entity type. It is derived from the state of the custom model. This property is read-only. |
| key | [key](#key) | false | The primary key of the entity type. It is used to uniquely identify instances of this entity type. |
| properties | [property[]](#property) | false | The properties of the entity type. |

#### key
A key property is a unique identifier for clear identification of an entity. In principle, identifiers can be used to identify any objects from products to people to abstract concepts.

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| id | string (uuid) | false | The unique identifier of the key property (generated by the service). |
| name | string | false | The name of the key property. Together with the name of all other properties, it must be unique within the entity type. |
| type | [keyType](#keytype) | true | The type of the key property. It specifies the range of values for the key property and thus defines how an entity is uniquely identified. |
| description | string | true | An arbitrary string attached to the key property. Often useful for displaying it to users. |
| state | [stateType](#statetype) | true | The current state of this key property. It is derived from the state of the custom model. This property is read-only. |

#### property
Each entity type in a model has a set of properties. It is these properties that are typically of interest for the further use and processing of an entity.

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| id | string (uuid) | false | The unique identifier of the property (generated by the service). |
| name | string | false | The name of the property. Together with the name of the key property, it must be unique within the entity type. |
| required | boolean | true | Determines whether the property is an required property. |
| indexed | boolean | true | Determines whether the property is an indexed property. |
| type | [propertyType](#propertytype) | true | The type of the property. It determines the kind and range of values the property can store. |
| description | string | true | An arbitrary string attached to the property. Often useful for displaying it to users. |
| state | [stateType](#statetype) | true | The current state of this property. It is derived from the state of the custom model. This property is read-only. |

#### keyType
An enumeration type that contains the values that specify the key type.

| Name | Description |
| ---- | ----------- |
| guid | A 16-byte (128-bit) unique identifier |
| int32 | A signed 32-bit integer |
| int64 | A signed 64-bit integer |
| string | A sequence of UTF-8 characters |

#### propertyType
An enumeration type that contains the values that specify a property type.

| Name | Description |
| ---- | ----------- |
| binary | Binary data |
| boolean | A binary-valued logic value |
| byte | An unsigned 8-bit integer |
| dateTimeOffset | A date and time value with a fixed time-zone offset of +00:00 |
| decimal | A numeric value with fixed precision and scale |
| double | An IEEE 754 binary64 floating-point number |
| guid | A 16-byte (128-bit) unique identifier |
| int16 | A signed 16-bit integer |
| int32 | A signed 32-bit integer |
| int64 | A signed 64-bit integer |
| sByte | A signed 8-bit integer |
| single | An IEEE 754 binary32 floating-point number |
| string | A sequence of UTF-8 characters |
| date | A date value |
| list&lt;string&gt; | A list of strings |

#### stateType
An enumeration type that contains the values of different states that a state aware model element can have.

| Name | Description |
| ---- | ----------- |
| initial | The model element has an initial status and is a draft. This is the default value when creating the model element. |
| staged | The model element is staged. All instance data of the model element are kept in an intermediate storage area. |
| published | The model element is publicly available to end users and all instance data of the model element are kept in the production storage area. |


# Example
## customers
### List all customers
```http
GET custom/example/customers
```
Retrieves all customers.

#### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "value": [{
    "id": "49e086ee-d77e-4009-9332-bfc167e3632c",
    "name": "Linda Lindbergh"
  },{
    "id": "43e07423-d04a-4624-add9-126390b73f56",
    "name": "Bill Burr"
  }]
}
```
### Create a new customer
```http
POST custom/example/customers
```
Creates a new customer.

#### Request Body
```json
{
  "id": "916e6a4b-3fe2-4801-bc8d-b6aa3dfe970c",
  "name": "Jack Jones"
}
```
#### Success Response
A successful response will have a `201 Created` HTTP status code and, for example, the following response body:

```json
{
  "id": "916e6a4b-3fe2-4801-bc8d-b6aa3dfe970c",
  "name": "Jack Jones"
}
```
### Get customer details
```http
GET custom/example/customers({cId})
```
Retrieves the information of the given customer.

#### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cId | Path | string | true | The unique identifier of the customer. |
#### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "id": "916e6a4b-3fe2-4801-bc8d-b6aa3dfe970c",
  "name": "Jack Jones"
}
```
### Partially update a customer
```http
PATCH custom/example/customers({cId})
```
Updates an existing customer partially.

#### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cId | Path | string | true | The unique identifier of the customer. |
#### Request Body
```json
{
  "name": "Jack J. Jones"
}
```
#### Success Response
A successful response will have a `204 No Content` HTTP status code and will have no response body.

### Replace a customer
```http
PUT custom/example/customers({cId})
```
Replaces an existing customer.

#### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cId | Path | string | true | The unique identifier of the customer. |
#### Request Body
```json
{
  "id": "916e6a4b-3fe2-4801-bc8d-b6aa3dfe970c",
  "name": "Jack Jones"
}
```
#### Success Response
A successful response will have a `204 No Content` HTTP status code and will have no response body.

### Delete a customer
```http
DELETE custom/example/customers({cId})
```
Delete an existing customer.

#### Parameters
| Parameter | In | Type | Required | Description |
| --------- | --- | ---- | -------- | ----------- |
| cId | Path | string | true | The unique identifier of the customer. |
#### Success Response
A successful response will have a `200 OK` HTTP status code and, for example, the following response body:

```json
{
  "id": "916e6a4b-3fe2-4801-bc8d-b6aa3dfe970c",
  "name": "Jack Jones"
}
```
## Example Schema
### customer
A simple customer schema as an example for the API documentation

| Name | Type | Nullable | Description |
| ---- | ---- | -------- | ----------- |
| id | string (uuid) | false | Unique identifier of the customer |
| name | string | true | Name of the customer |


