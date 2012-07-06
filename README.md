#Siren: a hypermedia specification for representing entities

##Example

Below is a JSON Siren example of an order, including sub-entities.  The first sub-entity, a collection of products associated with the order, is an embedded link.  Clients may choose to automatically resolve linked sub-entities.  The second sub-entity is an embedded representation of customer information associated with the order.  The example also includes an action to remove the order and a set of links to navigate through a list of orders.


```
{
  "class": "order",
  "properties": { 
      "orderNumber": 42, 
      "itemCount": 3 
  },
  "entities": [
    { 
      "class": "products collection", 
      "rel": "http://x.io/rels/order-products", 
      "href": "http://api.x.io/orders/42/products"
    },
    {
      "class": "info",
      "rel": "http://x.io/rels/customer", 
      "properties": { 
        "customerId": "1234", 
        "name": "Peter Joseph"
      },
      "links": [
        { "rel": "self", "href": "http://api.x.io/customers/1234" }
      ]
    }
  ],
  "actions": [
    {
      "class": "remove-order",
      "title": "Remove Order",
      "method": "DELETE",
      "href": "http://api.x.io/orders/42"
    }
  ],
  "links": [
    { "rel": "self", "href": "http://api.x.io/orders/42" },
    { "rel": "next", "href": "http://api.x.io/orders/43" },
    { "rel": "up", "href": "http://api.x.io/orders" }
  ]
}
```

##Introduction

Siren is a hypermedia specification for representing entities.  As HTML is used for visually representing documents on a Web site, Siren is a format for presenting entities via a Web API.  Siren offers structures to communicate information about entities, actions for executing state transitions, and links for client navigation.  

Siren is intended to be a general specification of a generic media type that can be applied to other types that are not inherently hypermedia-powered.  The initial implementation is JSON Siren.  Other implementations, such as XML Siren, may also be implemented using the Siren specification.

All examples in this document are in the JSON Siren format.

##Entities

An Entity is a URI-addressable resource that has properties and actions associated with it.  It may contain sub-entities and navigational links.

###Entity

* class (describes the nature of the entity)
* properties (object)
* entities (array)
* links (should include a link to self)
* actions (array)
* title (optional, client may ignore)
  
###Sub-Entities
* rel

##Linking

Links represent navigational transitions.  Related entities are included inline as sub-entities of the entity itself.

###Relationships

class vs rel

##Actions

* class
* title (optional, client may ignore)
* method
* href
* type (media type of request body, if one exists. x-www-form-urlencoded on GET)
* fields