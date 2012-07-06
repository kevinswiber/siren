#Siren: a hypermedia specification for representing entities

##Example

Below is a JSON Siren example of an order, including sub-entities.  The first sub-entity, a collection of items associated with the order, is an embedded link.  Clients may choose to automatically resolve linked sub-entities.  The second sub-entity is an embedded representation of customer information associated with the order.  The example also includes an action to add items to the order and a set of links to navigate through a list of orders.


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
      "class": "info customer",
      "rel": "http://x.io/rels/customer", 
      "properties": { 
        "customerId": "pj123",
        "name": "Peter Joseph"
      },
      "links": [
        { "rel": "self", "href": "http://api.x.io/customers/pj123" }
      ]
    }
  ],
  "actions": [
    {
      "class": "add-item",
      "title": "Add Item",
      "method": "POST",
      "href": "http://api.x.io/orders/42/items",
      "type": "application/x-www-form-urlencoded",
      "fields": [
        { "name": "orderNumber", "type": "hidden", "value": "42" },
        { "name": "productCode", "type": "text" },
        { "name": "quantity", "type": "number" }
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

Siren is a hypermedia specification for representing entities.  As HTML is used for visually representing documents on a Web site, Siren is a specification for presenting entities via a Web API.  Siren offers structures to communicate information about entities, actions for executing state transitions, and links for client navigation.  

Siren is intended to be a general specification of a generic media type that can be applied to other types that are not inherently hypermedia-powered.  The initial implementation is JSON Siren.  Other implementations, such as XML Siren, may also be implemented using the Siren specification.

All examples in this document are in the JSON Siren format.

##Entities

An Entity is a URI-addressable resource that has properties and actions associated with it.  It may contain sub-entities and navigational links.

###Entity

####```class```

Describes the nature of an entity's content based on the current representation.  Possible values are implementation-dependent and should be documented.  Must be a value of space-separated tokens.  Optional.

####```properties```

A set of key-value pairs that describe the state of an entity.  In JSON Siren, this is an object such as ```{ "name": "Kevin", "age": 30 }```.  Optional.

####```entities```

A collection of related sub-entities.  If a sub-entity contains an ```href``` value, it should be treated as an embedded link.  Clients may choose to optimistically load embedded links.  If no ```href``` value exists, the sub-entity is an embedded entity representation that contains all the characteristics of a typical entity.  One difference is that a sub-entity may contain a ```rel``` attribute to describe its relationship to the parent entity.

In JSON Siren, this is represented as an array.  Optional.

####```links```

A collection of items that describe navigational links, distinct from entity relationships.  Link items should contain a ```rel``` attribute to describe the relationship and an ```href``` attribute to point to the target URI.  Link relations follow the Web Linking RFC.  Entities should include a link ```rel``` to ```self```.  In JSON Siren, this is represented as ```"links": [{ "rel": "self", "href": "http://api.x.io/orders/1234" }]```  Optional.

####```actions```

A collection of action objects.  See Actions.  Optional

####```title```
Descriptive text about the entity.  Optional.

  
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

##Usage Considerations

Siren supports a resource design style that doesn't have to be primarily CRUD-based.  A root entity may take ownership of facilitating changes to sub-entities via actions.  Using Siren, it's much easier to provide a task-based interface to your Web API.