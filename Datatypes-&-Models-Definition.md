==========

Parameters describe the inputs into an API operation.  They live in the `operation` object:

```json

"apis":[
  {
    "path": "/pet.{format}/{petId}",
    "description": "Operations about pets",
    "operations": [
      {
        "parameters":[
          {
            "paramType": "path",
            "name": "petId",
            "description": "ID of pet that needs to be fetched",
            "dataType": "integer",
            "format": "int64",
            "required": true,
            "minimum": 0,
            "maximum": 10
          }
        ],
        ...

```

The `parameters` array may be empty if the `operation` requires no parameters.  The fields are:

`paramType`.  This is the type of the parameter.  It can be only one of the following: 
`path`, `query`, `body`, `header` or `form`.

A description of the fields is:

`name`.  This is the unique name for the parameter.  Each `name` must be unique, even if
they are associated with different `paramType` values.  Other notes on the `name` field:

* If `paramType` is `body`, the `name` is used only for UI and codegeneration.

* If `paramType` is `path`, the `name` field must correspond to the associated path segment from the `path`
field in the `api` object

* If `paramType` is `query`, the `name` field corresponds to the query param name.

`description`.  This is the human-readable description for the parameter.

`dataType`.  For `path`, `query`, and `header` `paramType`s, this field must be a `primitive`.
For `body`, this can be a `complex` or `container` datatype.

`required`.  For `path`, this is always true.  Otherwise, this field tells the client
whether or not the field must be supplied.

`enum`.  This is an optional field which restricts the input to a list of allowable input values.  See the [datatypes](Datatypes-&-Models-Definition) section for more details.


When sending multiple values, the `array` type should be usedrich@vostro ruby-2.0.0-p247@rails32 ~/swagger-core.wiki $ cat Datatypes-\&-Models-Definition.md 
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
case, the property field can have an optional `enum` property, which supports a list of allowable values, or a minimum/maximum numeric `Range`.  For example:

```json
"Pet": {
  "id": "Pet",
  "description": "A pet is a person's best friend",
  "required": [
    "name",
    "id"
  ],
  "properties": {
    "status": {
      "type": "string",
      "description": "pet status in the store",
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
    },
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
  "uniqueItems": true,
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