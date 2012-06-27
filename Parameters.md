Parameters
==========

Parameters describe the inputs into an API operation.  They live in the `operation` object:

```

  apis:[
    {
      path: "/pet.{format}/{petId}",
      description: "Operations about pets",
      operations: [
        {
          parameters:[
            {
              paramType: "path",
              name: "petId",
              description: "ID of pet that needs to be fetched",
              dataType: "String",
              required: true,
              allowableValues: {
                max: 10,
                min: 0,
                valueType: "Range"
              },
              allowMultiple: false
            }
          ],
          ...

```

The `parameters` array may be empty if the `operation` requires no parameters.  The fields are:

`paramType`.  This is the type of the parameter.  It can be only one of the following: 
`path`, `query`, `body`, or `header`.

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

`allowableValues`.  This is an optional field which restricts the input to either a `List` or
`Range` of allowable input values.  See the [datatypes](datatypes) section for more details
on the `allowableValues` field.

`allowMultiple`.  For `query` params, this specifies that a comma-separated list of values can
be passed to the API.  For `path` and `body` types, this field cannot be true.