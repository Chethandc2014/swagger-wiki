==========

For swagger implementations with no server integration, you just need to produce and serve Resource Listings and Api Declarations from any HTTP server.  The deployment structure typically looks like this:

| Path | Description | 
|:---- |:-----------|
|/{path}/api-docs               | Resource Listing     |
|/{path}/api-docs/{api-name}    | 1st Api Declaration  |
|/{path}/api-docs/{api-name-2}  | 2nd Api Declaration  |
|/{path}/api-docs/{api-name-n}  | nth Api Declaration  |

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

Swagger will expect the Api Declarations to be provided under the following paths:

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

Swagger will expect the Api Declarations to be provided under the following paths:

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

* Do it by hand.  Grab your favorite JSON editor and write some JSON manually.  It can be tedious but it's not XML...

* Use a 3rd party tool.  Here are some tools that can help create swagger JSON easily (not all are completely swagger-spec compliant):

[Source 2 Swagger](https://github.com/solso/source2swagger).  This is a tool which reads comments in source code and generate the Swagger JSON.

[Swagger Maven Plugin](https://github.com/kongchen/swagger-maven-plugin/). This is a maven plugin which can generate the Swagger JSON after you simply running `mvn compile`.

[Swdogen](https://github.com/dotcy/swdogen). This is an another tool which reads comments and generate JSON but use javadoc-like notations for documenting. 

[Raw 2 Swagger](https://github.com/solso/raw2swagger).  This tool will look at HTTP logs and heuristically create Swagger JSON.  This can be great for documenting traffic to existing servers where you have no control over the source code.

[Soap UI](http://olensmar.blogspot.se/2013/05/soapui-swagger-true.html).  This is a plug-in for the very popular SOAP-UI tool which allows you to build APIs graphically and export their definitions in Swagger spec.

### I'm impatient, give me a sample

Clone swagger-core:

```text
git clone https://github.com/wordnik/swagger-core.git

cd samples/no-server

mvn jetty:run
```

While you can literally use *any* web server, this is using the maven plug-in to launch jetty on port 8000, since it's so simple to use.  The files under `src/main/webapp` are available at the root of the web server, and pointing swagger-ui to `http://localhost:8000/api-docs`.  Or you can go directly to `http://localhost:8000` to view the embedded swagger-ui.
