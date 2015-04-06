# Yet Another Hypermedia(ish) API specification

Yahapi is a basic hypermedia-enabled REST convention. Its primary goal is to simplify API decisions you might need to make while building backend API's.

Yahapi guides your API design to:

- … link resources to each other;
- … embed different resource types within your JSON response;
- … provide a single unified error format for you API;
- … promote you to add references to your API documentation in your JSON response;
- … define how to return partial results;
- … sort and paginate collections.

In the end Yahapi is just a list of preferences that make your API look nice, simple and consistent.

## Example

Assume the following response:

```
GET /orders/43983
{
    "oderId": 43983,
    "created": "2014-08-05 16:22:16",
    "modified": "2015-05-22 13:45:20",
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

The same resource in Yahapi might look something like this:

```
GET /orders/43983
{
    "id": 43983,
    "created": "2014-08-05T16:22:16Z",
    "modified": "2015-05-22T13:45:20Z",
    "userId": "914",
    "type": "order",
    "items": [
        {
            "id": "912332",
            "type": "orderItem",
            "productId": "EZ-21562",
            "amount": 3,
            "links": {
                "self": { "href": "/orders/43983/items/912332" },
                "product": { "href": "/products/EZ-21562" }
            }
        },
        {
            "id": "912333",
            "type": "orderItem",
            "productId": "AF-49841",
            "amount": 1,
            "links": {
                "self": { "href": "/orders/43983/items/912333" },
                "product": { "href": "/products/AF-49841" }
            }
        }
    ],
    "links": {
        "self": { "href": "/orders/43983" },
        "user": { "href": "/users/914" },
        "items": { "href": "/orders/43983/items" }
    },
    "meta": {
    	"retrievedOn": "2014-08-06T11:38:24.162Z"
	 }
}
```

Yahapi uses three reserved top-level keywords: `type`, `links` and `meta`. 

* **Type** identifies the object as a resource (e.g. `order`) or as an embedded resource (e.g. `orderItem`) and tells the client how to process that resource.
* **Links** is a collection of url's related to the resource that may be of interest to the client.
* **Meta** contains any meta-information about the resource and is used primarily for pagination.

# Why Yet Another Hypermedia type?

In a [world](http://json-ld.org/) [full](http://stateless.co/hal_specification.html) [of](https://github.com/kevinswiber/siren) [hypermedia](http://amundsen.com/media-types/collection/) [types](http://jsonapi.org/) why choose Yahapi? Truth is none of the current Hypermedia types has become a practical standard; they are either too complex or lack features to fully drive your API design. The hypermedia that comes closest to being a full  style- and hypermedia-guide is ([JSON API](http://jsonapi.org/)). While it beautifully designed it requires an advanced server and smart client to make fully use of. 

Yahapi is designed to keep things simple. Yahapi adds hypermedia controls with limited modifications to the "naive" JSON REST design, promoting readability and pragmatism.

In addition Yahapi provides a list of guidelines for pagination, sorting, partial results and error formats; this should save you time Googling for best practices and make for a more consistent API.

All this ought to make your API more fun to work with for your clients.

If you want to know more about the reasoning behind Yahapi visit our FAQ.

# Contribute!

Yahapi is a work in progress, if you feel like contributing open up an [issue](https://github.com/nielskrijger/yahapi/issues) to start off a discussion or create a [Pull Request](https://github.com/nielskrijger/yahapi/pulls) with your suggested changes.
