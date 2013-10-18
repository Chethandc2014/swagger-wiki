Are you new to Swagger and trying to understand how you can get started with your own Swagger implementation? Well, you are in the right place...

Before we get started, I should point out that this page is a Wiki -- if you find anything on this page misleading, or if there is something you wish was included that would have helped you, make some changes for the next person to make their lives easier.

## Introduction
Swagger is made up of three components:
- Server: hosts the REST APIs that you want to use.
- Client: uses the REST APIs from the server.
- UI: Reads a description of the APIs from the server and renders it as a webpage and an interactive sandbox to play with the APIs.

<!-- diagram of client, server, UI -->

### Server
The typical place to start with a Swagger implementation is with the server. A server will have some number of APIs as well as a api-docs url (something like http://yourhost.com/api-docs or http://myhost.com/some-random-path/api-docs.json, etc.). The api-docs URL is the starting point for Swagger. It contains a JSON description of the resources that are available. More on what the resource listing looks like and how to create one in just a minute.

### Client
The client is any application that wants to use the APIs on the server. The client is given a URL that points to the api-docs and converts the JSON into an object that can used to call the REST APIs. Clients are available in any number of languages, making it very easy to quickly implement REST APIs that have been documented with Swagger without requiring much new code. 

<!-- see here for a list of clients -->

### UI
Finally, the Swagger UI serves a double-purpose as documentation and a way to enable developers to play around with the REST APIs without actually having to write any code. The default Swagger UI can be seen at the [pet store demo](http://petstore.swagger.wordnik.com/). The default Swagger UI includes a box at the top of the screen for typing in the URL for any api-docs. Since the Swagger UI is completely dynamic, it will read in the api-docs from any site and render the API documentation and enable the calling of the REST APIs. The default Swagger UI can be installed through `npm install swagger-ui`, and the HTML templates can be modified to your liking.

## Creating api-docs  
(aka Resource Listing; aka Swagger specification)
The api-docs URL that typically lives on the server and is used by both the client and the Swagger UI is also referred to as a Swagger specification. The actual api-docs URL is a resource listing that describes the Swagger resources that are available, where they live, and how to use them.

There are four ways to create a Swagger specification, depending on which server you are using:
- **Automatically:** Some servers, such as [swagger-node-express](https://npmjs.org/package/swagger-node-express) and [swagger-play](https://github.com/wordnik/swagger-core/tree/master/modules/swagger-play2), will create both your REST APIs and your Swagger Specification for you at the same time. <!-- link to swagger-node-express and other similar packages -->
- **Code Gen:** Other servers require that you run a `codegen` tool on your source code, which translates the actual code into a Swagger specification. <!-- link to codegen tools and servers / languages that use them -->
- **Comments:** Still other packages will convert comments, similar to jsDoc, into a Swagger specification. <!-- link to jsDoc or other packages that use comments -->
- **By Hand:** And finally, you can always create your Swagger specification by hand.

The benefit of generating your Swagger specification automatically or through one of the code generation tools is that it guarantees that your documentation is always in sync with your implementation. Using comments or creating a Swagger specification by hand has the risk that you may forget to update the Swagger specification (or may make a mistake in updating it) so that the documentation doesn't truly reflect the latest API. Note that this is more than just a problem for understanding the code: since the clients use the Swagger specification to automatically interface to the server, clients will be broken if the specification is wrong.

### Automatic Swagger Specification Creation
### Codegen Swagger Specification Creation
The Swagger codegen can be found on Github: https://github.com/wordnik/swagger-core

### Comments Swagger Specification Creation
### Swagger Specification Creation By Hand
Get a text editor and get going... best of luck!

## Swagger Specification Overview
### Resource File
### API File
### Model