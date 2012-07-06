#Siren: a hypermedia specification for representing entities

##Example

Below is a JSON Siren example of an order, including sub-entities.  The first sub-entity, a collection of items associated with the order, is an embedded link.  Clients may choose to automatically resolve linked sub-entities.  The second sub-entity is an embedded representation of a status associated with the order.  The example also includes an action to cancel the order and a set of links to navigate through a list of orders.


```
{
  "class": "order",
  "properties": { 
      "orderNumber": 42, 
      "itemCount": 3,
      "status": "pending"
  },
  "entities": [
    { 
      "class": "items collection", 
      "rel": "http://x.io/rels/order-items", 
      "href": "http://api.x.io/orders/42/items"
    },
    {
      "class": "info status",
      "rel": "http://x.io/rels/order-status", 
      "properties": { 
        "value": "pending"
      },
      "links": [
        { "rel": "self", "href": "http://api.x.io/orders/42/status" }
      ]
    }
  ],
  "actions": [
    {
      "class": "cancel-order",
      "title": "Cancel Order",
      "method": "PUT",
      "href": "http://api.x.io/orders/42/status",
      "type": "application/x-www-form-urlencoded",
      "fields": [
        { "name": "value", "type": "hidden", "value": "cancelled" }
      ]
    }
  ],
  "links": [
    { "rel": "self", "href": "http://api.x.io/orders/42" },
    { "rel": "previous", "href": "http://api.x.io/orders/41" },
    { "rel": "next", "href": "http://api.x.io/orders/43" }
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

* class (describes the nature of an entity)
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

##Design Considerations

Siren supports a resource design style that doesn't have to be primarily CRUD-based.  A root entity may take ownership of facilitating changes to sub-entities.  Using Siren, it's much easier to provide a task-based interface for your Web API.