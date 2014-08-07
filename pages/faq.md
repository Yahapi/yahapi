# FAQ

## Why should I add hypermedia controls to my JSON response?

You might wonder why you need links (or "Hypermedia Controls" as they are known) in your service response. Martin Fowler explains these hypermedia controls as follows:

> The links give client developers a hint as to what may be possible next. It doesn't give all the information […] but at least it gives them a starting point as to what to think about for more information and to look for a similar URI in the protocol documentation. [Martin Fowler](http://martinfowler.com/articles/richardsonMaturityModel.html)

Adding hypermedia controls makes your API more fun to work with for your client. It also enables client software to traverse links by rather than hard-coding all that logic.

## Why not use HAL?

HAL is great and you should definitely give it a look. You might consider using Yahapi because:

* HAL prefixes reserved words with an udnerscore (e.g. `_links`) whereas Yahapi likes to treat links as normal elements of your API.
* HAL moves embedded objects to an `_embedded` property, whereas Yahapi allows you to keep embedded objects as nested objects directly in your representation. Separating embedded objects in your representation is in line with the hypermedia philosophy but renders API's difficult to read and unpragmatic, Yahapi focusses on readability and pragmatism more than on hypermedia.

## Why not use JSON API?

JSON API is a big inspiration for Yahapi. Why you might consider using Yahapi instead is:

* JSON API focusses heavily on a reserved `id` property and makes clever use of this to link resources to each other, whereas Yahapi leaves you free to pick your own identifier type. In many business domains identity schemes already exists (e.g. [EAN](http://en.wikipedia.org/wiki/International_Article_Number_(EAN))) which make for a more descriptive resource identifier. In addition a business key is often a compound key made up of two or more elements, in a JSON API this might be easily missed.
* Yahapi foucsses on readability and simple clients whereas JSON API focusses on the use of smart client:
> It is specifically focused around using those APIs with a smart client that knows how to cache documents it has already seen and avoid asking for them again. [FAQ](http://jsonapi.org/faq/)

## Why not use Yahapi?

Obviously we think Yahapi is great, but in one use case some Hypermedia type may work better than others. For example:

* If you're working with an existing API and can't afford to change anything other than adding properties you should look at [HAL](http://stateless.co/hal_specification.html).
* If you want client software that intelligently tries to cache requests and minimize redundant requests, look at [JSON API](http://jsonapi.org/). 
* If you need a very comprehensive and formal approach to linking relationships between resources, have a look at [JSON+LD](http://json-ld.org/).
* If you'd like to serialize requests and responses directly to persistable entities, look at [Siren](https://github.com/kevinswiber/siren).
* If your API focusses on collection resources and you want to dynamically provide new search capabilities to your clients, have a look at [Collection+JSON](http://amundsen.com/media-types/collection/).

There are many flavors of Hypermedia types, finding the right one can be a challenge. We found for many use cases we didn't really need any of the above and just wanted a pragmatic standard we could use. Yahapi is the result.

## Why not use Web Linking (RFC 5988)?

RFC 5988 enables hypermedia links within a HTTP `Link`-header, for example:

	Link: <http://example.com/TheBook/chapter2>; rel="previous";
         title="previous chapter"

Yahapi does not use these because;

1) Link headers are easy to miss when viewing the response in a web browser; by treating links as first-class citizens of your resource representation you really promote them to your client.

2) Link headers do not work well for embedded/nested resources, imagine the following response:

```
GET /orders/43983
{
    "id": 43983,
    "type": "order",
    "href": "https://api.example.com/orders/43983",
    "items": [
        {
            "id": "912332",,
            "href": "https://api.example.com/orders/43983/items/912332",
            "links": {
                "product": { "href": "https://api.example.com/products/EZ-21562" }
            }
        }
    ],
    "links": {
        "customer": { "href": "https://api.example.com/customers/914" },
        "items": { "href": "https://api.example.com/orders/43983/items" }
    }
}
```
Moving all these links to a single HTTP header is not pragmatic.

## Why support different types of resources in the same collection?

Often in a SOA or Microservices architecture lower-level services are unaware of the business processes in which they are used. This lack of context may render them quite abstract. Lower-level services may not actually interpret what they are working with, they might just keep a reference to them. 

Imagine an order system that pulls products from different types of services; its list of items may be represented as follows:

```
…
	"items": [
        {
            "type": "fresh-foods",
            "itemId": "FD-3495723",
            "links": {
                "self": { "href": "https://api.example.com/foods/FD-3495723" }
            }
        },
        {
            "type": "books",
            "itemId": "BK-645",
            "links": {
                "self": { "href": "https://api.example.com/books/BK-645" }
            }
        }
    ],
…
```
The resource collection is still homogeneous by listing items with identical properties. The `type`  is there to allow higher-level services to interpret the collection's content. If a business service responsible for automated food-ordering it will be interested in "fresh-foods" only and have no understanding of "books". Similarly a front-end client might decide based on the `type` to display items differently or filter them.

The `type` is the key element for clients to understand how to interpret that resource.

## Who have contributed to Yahapi?

The author of Yahapi is Niels Krijger <mailto:niels@kryger.nl>.

## How can I contribute?

Please do! Yahapi is hosted on [Github](https://github.com/nielskrijger/yahapi), if you feel like contributing open up an [issue](https://github.com/nielskrijger/yahapi/issues) to start off a discussion or create a [Pull Request](https://github.com/nielskrijger/yahapi/pulls) with your suggested changes.
