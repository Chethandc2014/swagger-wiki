Resource Listing
==========

The Resource Listing creates an inventory of all APIs.  For example:

```
{
  apiVersion: "0.2",
  swaggerVersion: "1.1",
  basePath: "http://petstore.swagger.wordnik.com/api",
  apis: [
    {
      path: "/pet.{format}",
      description: "Operations about pets"
    },
    {
      path: "/user.{format}",
      description: "Operations about user"
    }
  ],
  models [ ... ]
}
```

The above example demonstrates the following required fields:

`swaggerVersion`.  This field must be one of the supported [Swagger Versions](changelog).  Providing
 this gives the best compatibility between swagger clients and [Swagger UI](http://github.com/wordnik/swagger-ui) 

`basePath`.  This is the base path of the server declaring the apis.

`apis`.  This is an array of `api`s available to the caller, and contain the relative `path` to the
 [API declaration](Api-Declaration).  If no apis are available, an empty array
 should be returned.
 
 `description` is an optional but recommended field to describe what the api does.

#### Other Notes

The full path to the resource listing is the entry point to swagger--both UI and client
generators will be pointing to this location.  In the above example, a `.{format}` is contained
on the `path` element.  While this suffix is optional, it is recommended as many APIs provide
both JSON and XML formats.

* base path points to the location of the service and does not contain a trailing slash
* The `.{format}` syntax is not required.
* zero to many apis in api array
* an optional `apiVersion` is provided.  The intent of this field is to provide information about the api service itself.