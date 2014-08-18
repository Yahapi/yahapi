# Format

### Description

- **Date(s)**: 2014-08-06 (Created)
- **Status**: Work in progress

### Conventions

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

# 1 General

# 1.1 Media type JSON

The media type of a Yahapi document is **`application/json`**.

There are no plans to register a Yahapi-specific media type.

# 2 Single resource object

A Yahapi document **MUST** contain at least one resource. A minimum valid document is:

	{}

## 2.1 type

A resource object **SHOULD** have a `type` property:

	{
		"type": "product"
	}
	
If no `type` property is specified the resource `type` **MAY** be inferred by the client based on the collection name. In the following example the `type` might be interpreted as `product`:
	
	GET /products/1234
	{
		"id": "1234"
	}

Full resource representations of elements in the same collection **MAY** contain different properties only if their type is different as well.

	GET /products/9016
	{
		"type": "food",
		"id": 9016,
		"expirationDate": "2015-03-06",
	}
	
	GET /products/9017
	{
		"type": "non-food",
		"id": 9017,
	}
	
## 2.2 links

A resource object **SHOULD** contain a `links` property containing valid URL's keyed by their [relationship](http://www.iana.org/assignments/link-relations/link-relations.xml) to the resource. 

	{
		…
		"links": {
			"self": { "href": "https://api.example.com/orders/001342" },
			"payment": { "href": "https://api.example.com/payment/53415" }
		}
	}

Every resource object **SHOULD** contain a `links` property with a `self`-relationship.

### 2.2.1 links.href

Every `links` property **MUST** contain a `href`property (hypertext reference) with a valid URL.

A hypertext reference **SHOULD** contain an absolute url. 

Using absolute urls enables the client to fully resolve the hypertext reference rather than having to prefix an assumed domain.

## 2.3 meta
A resource **MAY** contain a `meta` property.


# 3 Collection resource

A collection resource groups resource objects and may support ordering them, filtering them and/or paginating through them.

## 3.1 Element properties

A collection resource **MUST** be homogeneous and contain only elements with the same properties. 

*The following is invalid if either `items.name` or `items.id` are mandatory*:

	{
		"items": [
			{ name: "John" },
			{ id: 1234 }
		]
	}

## 3.2 Element type

A collection resource **MAY** contain different `types` if all types share the same abstraction. In the following example all items share the same abstract `product`:

	{
		"products": [
			{
				"type": "food",
				"id": 4897884,
				"links": {
					"self": { "href": "https://api.example.com/products/4897884" }
				}
			},
			{
				"type": "non-food",
				"id": 9016,
				"links": {
					"self": { "href": "https://api.example.com/products/9016" }
				}
			}
		]
	}

## 3.3 Pagination

A collection resource too large to fit in a single message usually supports some way to paginate through the collection results.

A response of a paginated collection resource looks something like this:

	GET /products?offset=20&limit=10
	
	{
		"products": [ … ],
		"links": {
			"next": { "href": "https://api.example.com/products?offset=30&limit=10" },
			"prev": { "href": "https://api.example.com/products?offset=10&limit=10" }
		},
		"meta": {
			"total": 46,
			"limit": 10,
			"offset": 20
		}
	}

### 3.3.1 URL request parameters

A paginated collection resource **SHOULD** support `offset` and `limit` url query parameters to paginate through a collection resource.

	GET /products?offset=20&limit=10

### 3.3.2 links.next and links.previous

A paginated collection resource **MUST** return `links.next` and `links.prev` to paginate through the collection unless there is no next or previous page.

### 3.3.3 meta.total

A paginated collection resource **SHOULD** return the number of items available in the collection using `meta.total`.

### 3.3.4 meta.limit

A paginated collection resource **SHOULD** return the number of records returned using `meta.limit`.

### 3.3.5 meta.offset

A paginated collection resource **SHOULD** return the number of items skipped using `meta.offset`.

## 3.4 Ordering

There are no strict rules about the *default* ordering of a collection. A collection may be ordered by default in ascending or descending order for any property that makes the most sense for your use case.

### 3.4.1 Client sort

A collection resource **MAY** support client sort in a collection by adding a `sort` parameter to the query string.

	GET /products?sort=type

### 3.4.2 Ascending or descending

The default order of a `sort` query parameter **MUST** be ascending.

To sort a collection in descending order the value of a `sort` query parameter **MUST** start with a minus symbol (`-`).

	GET /products?sort=-expirationDate

### 3.4.3 Multiple sort criteria

A collection resource supporting multiple sort criteria **SHOULD** allow multiple criteria in the `sort` query string separated by comma's:

	GET /products?sort=type,-expirationDate

The query string in the example above sorts products by `type` in ascending order first, and sorts all items of the same `type` by `expirationDate` in descending order.

# 4 Embedded resources

## 4.1 Embedded resource object identity
An embedded resource object MUST have at least a `type`, `links` or `meta` property or be referenced within the parent resource object in the their `links` with an identical name.

	GET /persons/john
	
	{
		"name": "John",
		"address": {
			"street": "221A Baker Street",
			"type": "home-address"
		},
		"links": {
			"self": { "href": "/persons/john" },
			"address": { "href": "https://api.example.com/addresses/16342" }
		}
	}

The example above shows a resource `person` with an embedded resource `address`. The `address` is identified as a resource object based on the presence of a `type` property and because a `links` relationship exists with the same name as the object property.

> Note: top-level resources are always identified as a resource and do not have these requirements.

## 4.2 Embedded collection resource

All elements of an embedded resource collection **MUST** have at least one `type`, `links` or `meta` property.

An embedded collection resource **SHOULD NOT** support pagination.

An embedded collection resource **SHOULD NOT** support ordering.

An embedded collection resource should be kept simple and contain either the entire or most relevant subset of items. Embedding a collection resource is an optimization to support the most common use cases for your API, no more. Uncommon use cases should query the entire collection resource.

# 5. Errors

An error object **SHOULD** be returned when the HTTP status code is in the 400 or 500 range.

Error details **SHOULD** be wrapped in an `error` object.

Example:

	{
		"error": {
			"status": 400,
			"code": "ValidationError",
			"message": "One or more request parameters are invalid",
			"errors": [
				{
					"code": "INVALID_TYPE",
					"path": "/parentId",
					"message": "invalid type: string (expected number)"
				}, {
					"code": "NUMBER_MAXIMUM_DECIMALS",
					"path": "/amount",
					"message": "must have no more than 2 decimals"
				}
			]
		}
	}

A service **MAY** adopt a different error format.

## 5.1 error.status

An error **SHOULD** contain a `status` property which is identical to the HTTP response code.

## 5.2 error.code

An error **SHOULD** contain a `code` property with an error description suitable for machine interpretation.

## 5.3 error.message

An error **SHOULD** contain a `message` property with an error description in a human-readable format.

## 5.4 error.errors[]

An error **MAY** contain an `errors` property with a list of sub-errors.

Sub-errors are used when multiple errors were detected that fall within the same category.

A sub-error is a normal error but **SHOULD NOT** contain `status` or `errors` properties.

## 5.5 error.path

An error **MAY** contain a `path` property to let the client identify which request parameter was the cause of the problem.

Nested objects in `error.path` are separated by a slash (e.g. `/root/nested/property`).

Array elements in `error.path` are identified by their index between brackets starting with zero (e.g. `/root/children[0]/name`).

	{
		"files": [
			{
				"id": "123",
				"extension": "png"
			},
			{
				"id": 56,
				"extension": "png"
			}
		]
	}

The request above might return the following error response:

	{
		"error": {
			"status": 400,
			"code": "ValidationError",
			"message": "One or more request parameters are invalid",
			"errors": [
				{
					"code": "INVALID_TYPE",
					"path": "/files[1]/id",
					"message": "invalid type: type (expected string)"
				}
			]
		}
	}

# 6. Style and encoding

## 6.1 Use UTF-8

Your character encoding **MUST** be UTF-8.

	Content-Type: application/json; charset=utf-8

## 6.1 lowerCamelCase or snake_case

Your property names **SHOULD** should be written in [lowerCamelCase](http://en.wikipedia.org/wiki/CamelCase) or [snake_case](http://en.wikipedia.org/wiki/Snake_case).

Your property names **MUST** be written in a consistent format.

## 6.2 Property names must be predictable

Your property names **MUST** be predictable.

Property names may be optional but you must avoid property names that are dynamically generated by the context.

Predictable API's are easier to parse in static languages and are better supported in technologies such as [JSON Path](http://goessner.net/articles/JsonPath/) and [JSON Schema](http://json-schema.org/).

## 6.3 Date format

All dates **MUST** be formatted using [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

Avoid unix timestamps.

## 6.4 Use HTTPS

Your API **SHOULD** use HTTPS encrypting with SSL/TLS.

## 6.5 Cross-domain requests

If you need to support cross-domain requests you **SHOULD** use CORS, not JSONP.

Only if you need to support old versions of a browser without CORS support you may have to use JSONP instead.

## 6.6 Reserved words

Javascript reserved keywords **SHOULD** be avoided;

    boolean break byte
    case catch char class const continue
    debugger default delete do double
    else enum export extends
    false final finally float for function
    goto
    if implements import in instanceof int interface
    let long
    native new null
    package private protected public
    return
    short static super switch synchronized
    this throw throws transient true try typeof
    var volatile void
    while with
    yield

# Update History
2014-08-06: First draft