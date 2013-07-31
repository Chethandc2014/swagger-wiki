==========

For swagger implementations with no server integration, you just need to produce and serve Resource Listings and Api Declarations from any HTTP server.  The depoyment structure typically looks like this:

##### HTTP Server
` + /{path}/api-docs`               <= this is the Resource Listing
` + /{path}/api-docs/{api-name}`    <= this is one Api Declaration
` + /{path}/api-docs/{api-name-2}`  
` + /{path}/api-docs/{api-name-n}`  <= this is the nth Api Declaration

In the above structure, the resource listing contains pointers to Api Declarations on the same server.  The Api Declaration can live on other servers as well, since the `path` to them can be absolute.  For example:

#### Resource Listing on single server
Assuming this JSON is available under `http://{host}/{path}/api-docs`:

```json
{
  "apiVersion": "1.0.0",
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

Swagger will expect the Api Declarations are provided under the following paths:

`http://{host}/{path}/api-docs/pet`

`http://{host}/{path}/api-docs/user`

#### Resource Listing on multiple servers
Assuming this JSON is available under `http://{host}/{path}/api-docs`:

```json
{
  "apiVersion": "1.0.0",
  "swaggerVersion": "1.2",
  "apis": [
    {
      "path": "http://api.myhost.com/api-docs/pet",
      "description": "Operations about pets"
    },
    {
      "path": "http://api.myhost.com/api-docs/user",
      "description": "Operations about user"
    }
  ]
}
```

Swagger will expect the Api Declarations are provided under the following paths:

`http://api.myhost.com/api-docs/pet`

`http://api.myhost.com/api-docs/user`

#### Api Declaration, and calling a Real Server

In all cases, whether using a server integration or not, the Api Declaration needs to have a `basePath` value which points to a real server, assuming you want to actually make calls to it.  The no-server integration will of course *not* respond to api calls if there's no API to back it!

In the above examples, assume there is an Api Declaration at `http://api.myhost.com/api-docs/pet`.  In that declaration (in addition to all the operation documentation), the `basePath` value would point to the real API server that is being documented, such as `http://petstore.swagger.wordnik.com/api`.  Then, the clients can call the server directly with requests:

```json
{
  "apiVersion": "1.0.0",
  "swaggerVersion": "1.2",
  "basePath": "http://petstore.swagger.wordnik.com/api",
  "resourcePath": "/pet",
  ...
```

Now all documentation lives on `http://api.myhost.com` but the calls are made to `http://petstore.swagger.wordnik.com/api`!  That means you can easily document your server without having to modify anything in the runtime.

### Producing Swagger JSON

There are a lot of ways to do this.