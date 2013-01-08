API Declaration
==========

The API Declaration provides all information about the API itself.  It contains base server
information as well as two sections: `apis` and `models`.

While it might seem repetitive, it's important to include the following base server fields:

```
{
  apiVersion: "0.2",
  swaggerVersion: "1.1",
  basePath: "http://petstore.swagger.wordnik.com/api",
  resourcePath: "/pet.{format}"
  
  ...

  apis: [...]
  models: {...}
}
```

Note in the above example there is a `resourcePath` element which is essentially the relative path
to the the declaration being shown.  The `basePath` field is very important--while it's very common
to have the Resource Listing and API Declarations on the server providing the APIs themselves, it
is NOT required--in fact, for situations where the server does not automatically generate the Swagger
Specification, a complete set of declarations can be provided as static files.  One can even
generate the Swagger Specification files for existing, non-Swagger services (you of course need to
manually figure out how the server behaves first).

 
### APIs

Each `api` is declared in this array.  The `path` field uniquely defines an api, and it should be 
unique in the array.  Multiple `operations` exist in an `api`, and can represent the different HTTP 
operations.

An example api is below, with some sections omitted:

```
  apis:[
    {
      path:"/pet.{format}/{petId}",
      description:"Operations about pets",
      operations:[
        {
          httpMethod:"GET",
          nickname:"getPetById",
          responseClass:"Pet",
          parameters:[ ... ]
          summary:"Find pet by its unique ID"
          notes: "Only Pets which you have permission to see will be returned",
          errorResponses:[ ... ]
        }
      ]
    }
```

In this `api`, there is one `operation`.  If there are no operations in the `api`, an empty array should be
returned.  The fields in the operation are:

`httpMethod`.  This is the HTTP method required to invoke this operation--the allowable values are `GET`, `POST`, `PUT`, `DELETE`.

`nickname`.  This is an optional field provided by the server for the convenience of the UI and client
code generator.

`responseClass`.  This is what is returned from the method--in short, it's either `void`, a `primitive`, a `complex` or a `container`
return value.  See the [datatypes](datatypes) section for more detail on supported datatypes.

`parameters`.  These are the inputs to the operation--see the [parameters](parameters) section for details.  If
there are no required or optional `parameters` for an operation, an empty array should be returned.

`summary`.  This is a short summary of what the operation does.  For maximum readability in the 
[swagger-ui](http://github.com/wordnik/swagger-ui), this field should be less than 60 characters

`notes`.  A longer text field to explain the behavior of the operation.

`errorResponses`.  An array describing the `error` cases returned by the operation.  See the [errors](errors) section for more detail
on this object.

Alongside the `apis` is a `models` array.  This contains a listing of all `non-primitive` datatypes required by ALL
apis described in this particular API Declaration.  Note!  Models declarations may repeat across different API Declarations.
Each declaration should function in a stand-alone fashion and provide all information necessary.

All HTTP operations for a particular `path` should be grouped in a single `api` object.  There should be no
duplicate HTTP methods for a single path.