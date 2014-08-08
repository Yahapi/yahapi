# Embedded collection resource

An embedded collection resource is a collection within a single resource object or another collection resource.

Embedded collection resources are primarily used to minimize network requests between client and server.

Embedded collection resources should be used for the most common requests to your resources and should be kept simple. Yahapi does not support paginating or sorting embedded collection resources.

**Example**

This example shows an `order` with an embedded collection resource `orderItems`.

```
GET /orders/43983
{
    "id": 43983,
    "createdAt": "2014-08-05T16:22:16.992Z",
    "customerId": "914",
    "type": "order",
    "orderItems": [
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
    	"retrievedOn": "2014-08-06T11:38:24.162Z"
	 }
}
```