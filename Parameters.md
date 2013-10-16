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

`enum`.  This is an optional field which restricts the input to a list of allowable input values.  See the [datatypes](datatypes) section for more details.


When sending multiple values, the `array` type should be used