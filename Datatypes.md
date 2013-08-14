Datatypes
==========

Swagger Spec supports `primitive`, `complex` and `container` values for both input and output.  Any datatype
which is not a `primitive` must be defined in the `models` section of the API Declaration.

#### Primitives

Since different programming languages represent primitive values differently, the Swagger spec supports only the following simple types, per JSON-schema draft 4:

| Common name | Swagger spec 1.2  |
|:----------- |:----------------- |
| integer     | integer, int32    |
| long        | integer, int64    |
| float       | number, float     |
| double      | number, double    |
| string      | string            |
| byte        | string, byte      |
| boolean     | boolean           |
| date        | string, date      |
| dateTime    | string, date-time |


Note that `simple` and `complex` names are case-sensitive.

#### Complex Types

The specification supports any complex type as long as it is declared in the `models` section.  The format
for declaring a complex type follows [JSON Schema draft v4](http://json-schema.org/) and is shown in the following example:

```json
"Tag": {
  "id": "Tag",
  "required": ["id"],
  "properties": {
    "name": {
      "type": "string"
    },
    "id": {
      "type": "integer",
      "format": "int64"
    }
  }
}
```

In the simple `model` above, the `Tag` model has two fields--`id` of type `integer` and `name` of type `string`.  
The `id` field in the model provides the `model` name, in this case `Tag`.  Note that `id` is required.

The `property` has an optional human-readable `description`.  It can also contain complex types--however
the `model` description should be flat: that is, references to complex types are valid but 
they should be declared in the `models` array.  For example:

```json
"Pet": {
  "id": "Pet",
  "description": "A pet is a person's best friend",
  "required": [
    "name",
    "id"
  ],
  "properties": {
    "name": {
      "type": "string"
    },
    "id": {
      "type": "integer",
      "format": "int64"
    },
    "tags": {
      "type": "array",
      "items": {
        "$ref": "Tag"
      }
    }
```

Here the `Pet` model has a `tag` property which is the complex type `Tag` from above.  

Some model properties may be restricted to a fixed set of values.  To support this use
case, the property field can have an optional `enum` property, which supports a list of alloable values, or a mimimum/maximum numeric `Range`.  For example:

```json
"Pet": {
  "id": "Pet",
  "description": "A pet is a person's best friend",
  "required": [
    "name",
    "id"
  ],
  "status": {
    "type": "string",
    "description": "pet status in the store",
    "enum": [
      "available",
      "pending",
      "sold"
    ]
  },
  "happiness": {
    "type": "integer",
    "format": "int32",
    "description": "how happy the Pet appears to be, where 10 is 'extremely happy'",
    "minimum": 1,
    "maximum": 10
  }
  ...

```

Putting information like this in the model declaration gives the consumer a better chance
to provide compatible models to the server.

#### Containers

Containers can hold `primitive` or `complex` datatypes.  The supported list of containers is:

An ordered list of values:

```json
{
  "type": "array",
  "items": {
    ...
  }
}
```

A unique `set` of values:

```json
{
  "type": "array",
  "uniqueItems" true,
  "items": {
    ...
  }
}
```

Containers must hold primitive or complex datatypes--nested containers are not supported.  Containers are defined in the json description as follows:

```json
"Pet":{
  "id": "Pet",
  "properties":{
  ...
    "categories":{
      "type":"array",
      "items":{
         "$ref":"Category"
      },
      "description":"categories that the Pet belongs to"
    }
```

In this case, the `Pet` has an attribute called `categories` which is of type `List of Category` objects.