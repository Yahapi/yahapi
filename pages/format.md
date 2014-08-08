# Format

### Description

- **Date(s)**: 2014-08-06 (Created)
- **Status**: Work in progress

### Conventions

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

# 1. Media type JSON

The media type of a Yahapi document is **`application/json`**.

# 2. Single resource object

A Yahapi document **MUST** contain at least one resource. A minimum valid document is:

	{}

## 2.1. type

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
	
## 2.2. links

A resource object **SHOULD** contain a `links` property containing valid URL's keyed by their [relationship](http://www.iana.org/assignments/link-relations/link-relations.xml) to the resource. 

	{
		…
		"links": {
			"self": { "href": "https://api.example.com/orders/001342" },
			"payment": { "href": "https://api.example.com/payment/53415" }
		}
	}

Every resource object **SHOULD** contain a `links` property with a `self`-relationship.

## 2.3 meta
A resource **MAY** contain a `meta` property.

# 4 Collection resource

## 4.1 Element properties

A collection resource **MUST** be homogeneous and contain only elements with the same properties. 

*The following is invalid*:

	{
		"items": [
			{ name: "John" },
			{ id: 1234 }
		]
	}

## 4.2 Element type

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

## 4.3 Pagination

### 4.3.1

## 4.4 Ordering

There are no strict rules about the default ordering of a collection, it may be ascending or descending and for any property that makes the most sense for your use case.

### 4.4.1 Client specified sort

A collection resource **MAY** support sorting criteria in a collection by adding a `sort` parameter to the query string.

	GET /products?sort=type

### 4.4.2 Ascending and descending

The default order of a `sort` query parameter **MUST** be ascending.

To sort a collection in descending order the value of a `sort` query parameter **MUST** start with a minus symbol (`-`).

	GET /products?sort=-expirationDate

### 4.4.3 Multiple sort criteria

A collection resource supporting multiple sort criteria **SHOULD** allow multiple criteria in the `sort` query string separated by comma's:

	GET /products?sort=type,-expirationDate

The query string in the example above sorts products by `type` in ascending order first, and sorts all items of the same `type` by `expirationDate` in descending order.

# 5 Embedded resources

## 5.1 Embedded resource object identity
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

## 5.2 Embedded collection resource

All elements of an embedded resource collection **MUST** have at least one `type`, `links` or `meta` property.

An embedded collection resource **SHOULD NOT** support pagination.

An embedded collection resource **SHOULD NOT** support ordering.

An embedded collection resource should be kept simple and contain either the entire or most relevant subset of items. Embedding a collection resource is an optimization to support the most common use cases for your API, no more. Uncommon use cases should query the entire collection resource.

# 6. Style

## 6.1. lowerCamelCase and snake_case

Your API **SHOULD** use [lowerCamelCase](http://en.wikipedia.org/wiki/CamelCase) or [snake_case](http://en.wikipedia.org/wiki/Snake_case) for resource property names.

Your API **MUST** maintain a consistent format.

# Update History
2014-08-06: First draft