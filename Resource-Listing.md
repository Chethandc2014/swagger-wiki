==========

The Resource Listing creates an inventory of all APIs and is provided by default at the `/api-docs.json` path.  For example:

```json
{
  "apiVersion": "0.2",
  "swaggerVersion": "1.2",
  "apis": [
    {
      "path": "/pet",
      "description": "Operations about pets"
    },
    {
      "path": "/user",
      "description": "Operations about user"
    }
  ]
}
```

The above example demonstrates the following required fields:

`swaggerVersion`.  This field must be one of the supported [Swagger Versions](changelog).  Providing
 this gives the best compatibility between swagger clients and [Swagger UI](http://github.com/wordnik/swagger-ui) 

`apis`.  This is an array of `api`s available to the caller, and contain the relative `path` to the
 [API declaration](Api-Declaration).  If no apis are available, an empty array
 should be returned.
 
 `description` is an optional but recommended field to describe what the api does.

#### Other Notes

The full path to the resource listing is the entry point to swagger--both UI and client
generators will be pointing to this location.

* zero to many apis in api array
* an optional `apiVersion` is provided.  The intent of this field is to provide information about the api service itself.
* Specify the `swaggerVersion` that you are using.  It helps the swagger-ui and codegen tools parse the different structures.