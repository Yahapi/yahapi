# Collection resource

A collection resource contains a list of resources.

**Example**

This example shows an `order` with an embedded collection resource `orderItems`.

```
GET /orders/43983/items?offset=10&limit=10
{
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
        "self": { "href": "https://api.example.com/orders/43983/items" }
    },
    "meta": {
      "offset": 10,
      "limit": 10,
      "total": 284,
      "retrieved": "2014-08-06T11:38:24.162Z"
   }
}
```

### TODO add next/previous/first/last links...
