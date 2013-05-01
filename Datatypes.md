Datatypes
==========

Swagger Spec supports `primitive`, `complex` and `container` values for both input and output.  Any datatype
which is not a `primitive` must be defined in the `models` section of the API Declaration.

#### Primitives

Since different programming languages represent primitive values differently, the Swagger spec supports only the
following standard values:

```
Swagger Type     Description
----------------------------
byte             
boolean          
int             
long             
float            
double           
string           
Date             a ISO-8601 Date, which is represented in a String (1970-01-01T00:00:00.000+0000)

```

Note that `primitive` and `complex` names are case-sensitive.

#### Complex Types

The specification supports any complex type as long as it is declared in the `models` section.  The format
for declaring a complex type follows [JSON Schema](http://json-schema.org/) and is shown in the following example:

```json

    "Tag": {
      "properties":{
        "id":{
          "type": "long",
          "description": "unique identifier for the tag"
        },
        "name":{
          "type": "string"
        }
      },
      "id":"Tag"
    }

```

In the simple `model` above, the `Tag` model has two fields--`id` of type `Long` and `name` of type `String`.  
The `id` field in the model provides the `model` name, in this case `Tag`.

The `property` has an optional human-readable `description`.  It can also contain complex types--however
the `model` description should be flat: that is, references to complex types are valid but 
they should be declared in the `models` array.  For example:

```json

    "Pet":{
      "properties":{
        "tag":{
          "type":"Tag",
        },
        "id":{
          "type":"long"
        },
        ...
    }

```

Here the `Pet` model has a `tag` property which is the complex type `Tag` from above.  

Some model properties may be restricted to a fixed set of values.  To support this use
case, the property field can have an optional `allowableValues` property, which supports both
a `List` of values or a numeric `Range`.  For example:

```json

    "Pet":{
      "id":"Pet",
      "properties":{
      ...
        "status":{
          "type":"String",
          "description":"pet status in the store",
          "allowableValues":{
            "valueType":"LIST",
            "values":[
              "available",
              "pending",
              "sold"
            ]
          }
        },
        "happiness": {
          "type": "int",
          "description": "how happy the Pet appears to be, where 10 is 'extremely happy'",
          "allowableValues": {
            "valueType": "RANGE",
            "min": 1,
            "max": 10
          }
        },
        ...

```

Putting information like this in the model declaration gives the consumer a better chance
to provide compatible models to the server.

#### Containers

Containers can hold `primitive` or `complex` datatypes.  The supported list of containers is:

`List`.  An ordered list of values

`Set`.  An unordered set of unique values

`Array`.  An unordered list of values

Containers must hold primitive or complex datatypes--nested containers are not supported.  Containers are defined in the json description as follows:

```json
    "Pet":{
      "id": "Pet",
      "properties":{
      ...
        "categories":{
          "type":"List",
          "description":"categories that the Pet belongs to",
          "items":{
             "$ref":"Category"
          }
        }
```

In this case, the `Pet` has an attribute called `categories` which is of type `List of Category` objects.