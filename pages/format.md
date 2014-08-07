# Format

### Description

- **Date(s)**: 2014-08-06 (Created)
- **Status**: Work in progress

### Conventions

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).

# 1. Media type JSON

Currently no media type has been registered for Yahapi.

# 2. Top-level

A Yahapi document MUST contain at least one resource. A minimum valid document is:

	{}

## 2.1. type
A resource MAY have a `type` property:

	{
		"type": "product"
	}
	
If no `type` property is specified the resource `type` can be inferred by the client based on the collection name. In the following example the `type` would be `product`:
	
	GET /products/1234
	{
		"href": "/products/1234"
	}

A resource collection MUST be homogeneous and MAY contain different `types` if all types share the same abstraction. In the following example all items share the abstract `product`:

	{
		"products": [
			{
				"type": "food",
				"id": 4897884,
				"href": "https://api.example.com/products/4897884"
			},
			{
				"type": "non-food",
				"id": 9016,
				"href": "https://api.example.com/products/9016"
			}
		]
	}

Full resource representations within the same collection MAY contain different properties only if their type is different as well.

	GET /products/9016
	{
		"type": "food",
		"id": 4897884,
		"expirationDate": "2015-03-06",
		"href": "https://api.example.com/products/4897884"
	}
	
	GET /products/9016
	{
		"type": "non-food",
		"id": 9016,
		"href": "https://api.example.com/products/9016"
	}
	
## 2.2. links
A resource SHOULD contain a `links` property containing valid URL's keyed by their [relationship](http://www.iana.org/assignments/link-relations/link-relations.xml):

	{
		"links": {
			"payment": { "href": "https://api.example.com/payment?order=53415" }
		}
	}

A `links` property SHOULD contain a `self`-relationship.

## 2.3 meta
A resource MAY contain a `meta` property

# 3 Embedded resources

## 3.1 embedded resource identity
An embedded resource MUST have at least one `type`, `links` or `meta` property.

# 5. References



# Update History
2014-08-06: First draft