#Siren: a hypermedia specification for representing entities

##Example

Below is a JSON Siren example of an order, including sub-entities.  The first sub-entity, a collection of items associated with the order, is an embedded link.  Clients may choose to automatically resolve linked sub-entities.  The second sub-entity is an embedded representation of customer information associated with the order.  The example also includes an action to add items to the order and a set of links to navigate through a list of orders.

The media type for JSON Siren is ```application/vnd.siren+json```.

```json
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

Root entities and sub-entities that are embedded representations MUST contain a ```links``` collection with at least one item contain a ```rel``` value of ```self``` and an ```href``` attribute with a value of the entity's URI.

Sub-entities that are embedded links MUST contain an ```href``` attribute with a value of its URI.

###Entity

####```class```

Describes the nature of an entity's content based on the current representation.  Possible values are implementation-dependent and should be documented.  Must be a value of space-separated tokens.  Optional.

####```properties```

A set of key-value pairs that describe the state of an entity.  In JSON Siren, this is an object such as ```{ "name": "Kevin", "age": 30 }```.  Optional.

####```entities```

A collection of related sub-entities.  If a sub-entity contains an ```href``` value, it should be treated as an embedded link.  Clients may choose to optimistically load embedded links.  If no ```href``` value exists, the sub-entity is an embedded entity representation that contains all the characteristics of a typical entity.  One difference is that a sub-entity may contain a ```rel``` attribute to describe its relationship to the parent entity.

In JSON Siren, this is represented as an array.  Optional.

####```links```

A collection of items that describe navigational links, distinct from entity relationships.  Link items should contain a ```rel``` attribute to describe the relationship and an ```href``` attribute to point to the target URI.  Entities should include a link ```rel``` to ```self```.  In JSON Siren, this is represented as ```"links": [{ "rel": "self", "href": "http://api.x.io/orders/1234" }]```  Optional.

####```actions```

A collection of action objects, represented in JSON Siren as an array such as ```{ "actions": [{ ... }] }```.  See Actions.  Optional

####```title```
Descriptive text about the entity.  Optional.

  
###Sub-Entities

Sub-entities can be expressed as either an embedded link or an embedded representation.  In JSON Siren, sub-entities are represented by an ```entities``` array, such as ```{ "entities": [{ ... }] }```.

####Embedded Link

A sub-entity that's an embedded link may contain the following:

#####```class```

Describes the nature of an entity's content based on the current representation.  Possible values are implementation-dependent and should be documented.  Must be a value of space-separated tokens.  Optional.

#####```rel```

Defines the relationship of the sub-entity to its parent, per [Web Linking (RFC5899)](http://tools.ietf.org/html/rfc5988).  Required.

#####```href```

The URI of the linked sub-entity.  Required.

####Embedded Representation

Embedded sub-entity representations retain all the characteristics of a standard entity, but may also contain a ```rel``` attribute describing the relationship of the sub-entity to its parent.

###Classes vs. Relationships

It's important to note the distinction between link relations and classes.  Link relations define a relationship between two resources.  Classes define a classification of the nature of the element, be it an entity or an action, in its current representation.

##Links

Links represent navigational transitions.  In JSON Siren, links are represented as an array inside the entity, such as ```{ "links": [{ "rel": "self", "href": "http://api.x.io/orders/42"}] }```

Links may contain the following attributes:

###```rel```

Defines the relationship of the link to its entity, per [Web Linking (RFC5899)](http://tools.ietf.org/html/rfc5988).  Required.

###```href```

The URI of the linked resource.  Required.

##Actions

Actions show available behaviors an entity exposes.

###```class```

Describes the nature of an action based on the current representation.  Possible values are implementation-dependent and should be documented.  Must be a value of space-separated tokens.  Optional.

###```method```

An enumerated attribute mapping to a protocol method.  For HTTP, these values may be ```GET```, ```PUT```, ```POST```, ```DELETE```, or ```PATCH```.  As new methods are introduced, this list can be extended.  If this attribute is omitted, ```GET``` should be assumed.  Optional.

###```href```

The URI of the action.  Required.

###```title```

Descriptive text about the action.  Optional.

### ```type```

The encoding type for the request.  When omitted and the ```fields``` attribute exists, the default value is ```application/x-www-form-urlencoded```.  Optional.

### ```fields```

A collection of fields, expressed as an array of objects in JSON Siren such as ```{ "fields" : [{ ... }] }```.  See Fields.  Optional.

###Fields

Fields represent controls inside of actions.  They may contain these attributes:

####name

A name describing the control.  Required.

####type

The field type.  This may include any of the [input types](http://www.w3.org/TR/html5/single-page.html#the-input-element) specified in HTML5.

Currently, the possible values are:

```hidden```, ```text```, ```search```, ```tel```, ```url```, ```email```, ```password```, ```datetime```, ```date```,
```month```, ```week```, ```time```, ```datetime-local```, 
```number```, ```range```, ```color```, ```checkbox```,
```radio```, ```file```, ```submit```, ```image```, ```reset```, ```button```

When missing, the default value is ```text```.  Optional.

####value

A value assigned to the field.  Optional.

##Usage Considerations

Siren supports a resource design style that doesn't have to be primarily CRUD-based.  A root entity may take ownership of facilitating changes to sub-entities via actions.  Using Siren allows you to easily provide a task-based interface through your Web API.

## Feedback

Siren is still a work in progress looking for some real world usage and feedback.  Please contribute!  Feel free to use GitHub Issues to make suggestions or fire up a Pull Request with changes to be reviewed.  Thank you!