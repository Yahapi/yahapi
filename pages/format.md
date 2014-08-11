# Format

### Description

- **Date(s)**: 2014-08-06 (Created)
- **Status**: Work in progress

### Conventions

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

# 1 Media type JSON

The media type of a Yahapi document is **`application/json`**.

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
		"href": "/products/1234"
	}

Full resource representations of elements in the same collection **MAY** contain different properties only if their type is different as well.

	GET /products/9016
	{
		"type": "food",
		"id": 9016,
		"expirationDate": "2015-03-06",
		"href": "https://api.example.com/products/9016"
	}
	
	GET /products/9017
	{
		"type": "non-food",
		"id": 9017,
		"href": "https://api.example.com/products/9017"
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

## 2.2.1 links.href

Every `links` property **MUST** contain a `href`property (hypertext reference) with a valid URL.

A hypertext reference **SHOULD** contain an absolute url. 

Using absolute urls enables the client to fully resolve the hypertext reference rather than having to prefix an assumed domain.

## 2.3 meta
A resource **MAY** contain a `meta` property.

# 3 Collection resource

## 3.1 Element properties

A collection resource **MUST** be homogeneous and contain only elements with the same properties. 

*The following is invalid*:

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

### 3.3.1

## 3.4 Ordering

There are no strict rules about the default ordering of a collection, it may be ascending or descending and for any property that makes the most sense for your use case.

### 3.4.1 Client sort

A collection resource **MAY** support client sort in a collection by adding a `sort` parameter to the query string.

	GET /products?sort=type

### 3.4.2 Ascending and descending

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

# 5. Error codes

## 5.1 error.code

An error **MUST** contain an error code which is usually the same as the HTTP response code.

	{
		"error": {
			"code": 404
		}
	}

## 5.2 error.message



## 5.3 error.errors[]

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

## 6.4 Use HTTPS

Your API **SHOULD** use HTTPS encrypting with SSL/TLS.

## 6.5 Cross-domain requests

If you need to support cross-domain requests you **SHOULD** use CORS, not JSONP.

Only if you need to support old versions of some browser you may have to use JSONP instead.

# Update History
2014-08-06: First draft