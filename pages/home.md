# Yet Another Hypermedia(ish) API specification

## Summary

If you're looking for a pragmatic Hypermedia-enabled REST convention for your project; Yahapi is the answer. Yahapi guides your API design to:

- … link resources to each other;
- … embed resources directly in your JSON response;
- … provide a single unified error format for your API;
- … promote you to add references to your API documentation in your JSON response;
- … define how to return partial results;
- … sort and paginate collection resources.

## Example

Assume the following naive JSON response:

```
GET /orders/43983
{
    "oderId": 43983,
    "createdAt": "2014-08-05T16:22:16.992Z",
    "userId": "914",
    "items": [
        {
            "orderItemId": "912332",
            "productId": "EZ-21562",
            "amount": 3
        },
        {
            "orderItemId": "912333",
            "productId": "AF-49841",
            "amount": 1
        }
    ]
}
```

Yahapi would like you to transform this to:

```
GET /orders/43983
{
    "oderId": 43983,
    "createdAt": "2014-08-05T16:22:16.992Z",
    "customerId": "914",
    "type": "order",
    "items": [
        {
            "orderItemId": "912332",
            "type": "orderItem",
            "productId": "EZ-21562",
            "amount": 3,
            "links": {
                "self": { "href": "https://api.example.com/orders/43983/items/912332" },
                "product": { "href": "https://api.example.com/products/EZ-21562" }
            }
        },
        {
            "orderItemId": "912333",
            "type": "orderItem",
            "productId": "AF-49841",
            "amount": 1,
            "links": {
                "self": { "href": "https://api.example.com/orders/43983/items/912333" },
                "product": { "href": "https://api.example.com/products/AF-49841" }
            }
        }
    ],
    "links": {
        "self": { "href": "https://api.example.com/orders/43983" },
        "customer": { "href": "https://api.example.com/customers/914" },
        "items": { "href": "https://api.example.com/orders/43983/items" }
    },
    "meta": {
    	"retrieved_on": "2014-08-06T11:38:24.162Z"
	 }
}
```

In this example no properties were changed or moved, they were only added. Also none of the new properties are mandatory allowing you to gradually migrate your existing API to Yahapi or just use the parts you need/like.

Yahapi uses three reserved top-level keywords: `type`, `links` and `meta`. 

* **Type** identifies the object as a resource (e.g. `order`) or as an embedded resource (e.g. `orderItem`) and tells the client how to process that resource.
* **Links** is a collection of url's that may be of interest to the client.
* **Meta** contains any meta-information about the resource, for example pagination.

# Why Yet Another Hypermedia type?

In a [world](http://json-ld.org/) [full](http://stateless.co/hal_specification.html) [of](https://github.com/kevinswiber/siren) [hypermedia](http://amundsen.com/media-types/collection/) [types](http://jsonapi.org/) why choose Yahapi? Truth is none of the current Hypermedia types has become a practical standard; they are either too complex or lack features to fully drive your API design. The hypermedia that comes close to being a full service style guide and hypermedia type ([JSON API](http://jsonapi.org/)) requires an advanced server and client to make fully use of. 

Yahapi is designed to keep things simple. Yahapi adds hypermedia controls with limited modifications to the "naive" JSON REST design, promoting readability and pragmatism.

In addition Yahapi provides a list of guidelines for pagination, sorting, partial results and error formats; this should save you time Googling for best practices and make for a more consistent API.

All this ought to make your API more fun to work with for your clients.

If you want to know more about the reasoning behind Yahapi visit our FAQ.