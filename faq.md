# FAQ

## Why should I add hypermedia controls to my JSON response?

You might wonder why you need links (or "Hypermedia Controls" as they are known) in your service response. Martin Fowler explains these hypermedia controls as follows:

> The links give client developers a hint as to what may be possible next. It doesn't give all the information […] but at least it gives them a starting point as to what to think about for more information and to look for a similar URI in the protocol documentation. [Martin Fowler](http://martinfowler.com/articles/richardsonMaturityModel.html)

Adding hypermedia controls makes your API more fun to work with for your client. It also enables client software to traverse links by rather than hard-coding all that logic.

## Why not use HAL?

[HAL](http://stateless.co/hal_specification.html) is great and you should definitely give it a look. You might consider using Yahapi because:

* HAL prefixes reserved words with an underscore (e.g. `_links`) whereas Yahapi likes to treat links as normal elements of your API.
* HAL moves embedded objects to an `_embedded` property, whereas Yahapi allows you to keep embedded objects as nested objects directly in your representation. Separating embedded objects in your representation is in line with the hypermedia philosophy but renders API's difficult to read and unpragmatic, Yahapi focusses on readability and pragmatism more than on hypermedia.

## Why not use JSON API?

[JSON API](http://jsonapi.org/) is a big inspiration for Yahapi. Why you might consider using Yahapi instead is:

* JSON API focusses heavily on a reserved `id` property and makes clever use of this to link resources to each other, whereas Yahapi leaves you free to pick your own identifier type. In many business domains identity schemes already exist (e.g. [EAN](http://en.wikipedia.org/wiki/International_Article_Number_(EAN))) and resources may consist of a compound key which make for a more expressive resource identifier than `id`. For example, Yahapi considers the following valid:

    GET /persons/john
    {
      "name": "John"
    }


* Yahapi focusses on readability and simple clients where JSON API focusses on the use of smart clients:

> JSON API is specifically focused around using those APIs with a smart client that knows how to cache documents it has already seen and avoid asking for them again. [JSON API FAQ](http://jsonapi.org/faq/)

## Why not use Yahapi?

Obviously we think Yahapi is great, but in one use case a particular Hypermedia type may work better than others. For example:

* If you're working with an existing API and can't afford to change anything other than adding new properties you should look at [HAL](http://stateless.co/hal_specification.html).
* If you want client software that intelligently tries to cache requests and minimize redundant requests, look at [JSON API](http://jsonapi.org/).
* If you need a very comprehensive and formal approach to linking relationships between resources, have a look at [JSON+LD](http://json-ld.org/).
* If you'd like to serialize requests and responses directly to persistable entities, look at [Siren](https://github.com/kevinswiber/siren).
* If your API focusses on collection resources and you want to dynamically provide new search capabilities to your clients, have a look at [Collection+JSON](http://amundsen.com/media-types/collection/).

There are many flavors of Hypermedia types, finding the right one can be a challenge. We found none of the above really fits a wide range of common use cases. Yahapi is our alternative.

## Why not use Web Linking (RFC 5988)?

[RFC 5988](http://tools.ietf.org/html/rfc5988) standardizes hypermedia links within a HTTP `Link`-header, for example:

  Link: <http://example.com/TheBook/chapter2>; rel="previous";
         title="previous chapter"

Yahapi does not use this because;

1. Link headers are easy to miss when viewing the response in a web browser or even in a curl. By treating links as part of your resource representation you really promote them to your client.

2. Link headers do not work well for embedded resources. Moving all links of the example below into a single HTTP header is not pragmatic.

        GET /orders/43983
        {
            "id": 43983,
            "customer_id": 914,
            "type": "order",
            "items": [
                {
                    "id": "9122",
                    "links": {
                        "self": { "href": "https://api.example.com/orders/43983/items/9122" },
                        "product": { "href": "https://api.example.com/products/EZ-21562" }
                    }
                }
            ],
            "links": {
                "self": { "href": "https://api.example.com/orders/43983" },
                "customer": { "href": "https://api.example.com/customers/914" },
                "items": { "href": "https://api.example.com/orders/43983/items" }
            }
        }


## Why support different types of resources in the same collection?

Make no mistake, collection resources must be homogeneous with regard to its element properties.

In a SOA or Microservices architecture lower-level services are often unaware of the business processes in which they are used. This lack of context may render them quite generic. Lower-level services may not actually interpret what they are working with, they might just keep a reference to them.

Imagine an order system that pulls products from different types of services; its list of items may be represented as follows:

```
…
  "items": [
        {
            "type": "fresh-foods",
            "item_id": "FD-3495723",
            "links": {
                "self": { "href": "https://api.example.com/foods/FD-3495723" }
            }
        },
        {
            "type": "books",
            "item_id": "BK-645",
            "links": {
                "self": { "href": "https://api.example.com/books/BK-645" }
            }
        }
    ],
…
```
The resource collection is homogeneous because it lists items with identical properties. The `type`  is there to allow higher-level services to interpret the collection's content. For example, if a business service is responsible for automated food-ordering it will be interested in "fresh-foods" only and have no understanding of "books". Similarly a front-end client might decide based on the `type` to display items differently or filter them.

The `type` is the key element for clients to understand how to interpret that resource.

## Why use a verbose "self" link rather than simply "href"?

A property `"href": "<url>"` replacing `"links": { "self": "<url>" }` would make your API easier to read, particularly if the `self` link is the only link returned by your resource or embedded resource.

Yahapi chose not to adopt `href` because:

1. `href` may already be in use by existing API's.
2. The convention of a `self`-link is widely promoted by most Hypermedia types.
3. It is more consistent for the client when all links are represented the same way.

## Why does Yahapi not choose between lowerCamelCase or snake_case?

There is no single standard for JSON API formats; [Google promotes lowerCamelCase](https://google-styleguide.googlecode.com/svn/trunk/jsoncstyleguide.xml#Property_Name_Format), [Amazon AWS favors UpperCamelCase](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/frames.html), [Facebook uses snake_case](https://developers.facebook.com/docs/), just like the [Twitter API](https://dev.twitter.com/docs/api) and the [US Government](https://github.com/18F/api-standards).

Both lowerCamelCase and snake_case are firmly embedded as API styles and no clear winner is emerging, only UpperCamelCase is considered non-standard.

## Why does Yahapi promote limit and offset rather than paged results using cursor?

There are roughly two ways to paginate through a collection, either by letting the client specify a page number or let the client set a limit and offset. Paging makes the most sense when there is a cursor held open (usually in the database), whereas limit and offset provide more flexibility to the client.

Most web applications requests are handled by stateless web services that do not keep cursors open, instead each new collection resource request is processed independently of one another. Stateless web services are considered best practice.

Because use cases that require the client to set a custom page size are fairly common Yahapi promotes paginating collection resources using limit/offset.

Most of the pagination API style is optional and you can adopt your own pagination style when needed.

## Who contributed to Yahapi?

The author of Yahapi is Niels Krijger (<mailto:niels@kryger.nl>).

## How can I contribute?

Please do!

Yahapi is hosted on [Github](https://github.com/nielskrijger/yahapi), if you feel like contributing open up an [issue](https://github.com/nielskrijger/yahapi/issues) to start off a discussion or create a [Pull Request](https://github.com/nielskrijger/yahapi/pulls) with your suggested changes.
