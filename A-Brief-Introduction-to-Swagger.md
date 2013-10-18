Are you new to Swagger and trying to understand how you can get started with your own Swagger implementation? Well, you are in the right place...

Before we get started, I should point out that this page is a Wiki -- if you find anything on this page misleading, or if there is something you wish was included that would have helped you, make some changes for the next person to make their lives easier.

## Introduction
Swagger is made up of three components:
- Server: hosts the REST APIs that you want to use.
- Client: uses the REST APIs from the server.
- UI: Reads a description of the APIs from the server and renders it as a webpage and an interactive sandbox to play with the APIs.

<!-- diagram of client, server, UI -->

#### Server
The typical place to start with a Swagger implementation is with the server. A server will have some number of APIs as well as a api-docs url (something like http://yourhost.com/api-docs or http://myhost.com/some-random-path/api-docs.json, etc.). The api-docs URL is the starting point for Swagger. It contains a JSON description of the resources that are available. More on what the resource listing looks like and how to create one in just a minute.

#### Client
The client is any application that wants to use the APIs on the server. The client is given a URL that points to the api-docs and converts the JSON into an object that can used to call the REST APIs. Clients are available in any number of languages, making it very easy to quickly implement REST APIs that have been documented with Swagger without requiring much new code. 

<!-- see here for a list of clients -->

#### UI
Finally, the Swagger UI serves a double-purpose as documentation and a way to enable developers to play around with the REST APIs without actually having to write any code. The default Swagger UI can be seen at the [pet store demo](http://petstore.swagger.wordnik.com/). The default Swagger UI includes a box at the top of the screen for typing in the URL for any api-docs. Since the Swagger UI is completely dynamic, it will read in the api-docs from any site and render the API documentation and enable the calling of the REST APIs. The default Swagger UI can be installed through `npm install swagger-ui`, and the HTML templates can be modified to your liking.

## Creating Your Swagger Specification
The api-docs URL that typically lives on the server and is used by both the client and the Swagger UI is  referred to as a Swagger Specification. The Swagger Specification is made up of three files:
- **Resource Listing:** Lists the APIs that are available and gives a brief description of them.
- **API Description:** Detailed description of each API in the Resource Listing, including both the functional description (parameters, function names, return values) and human-readable description of how to use the API.
- **Models:** JSON Schema descriptions that describe the objects that are passed in as parameters or returned from the APIs. If your APIs are simple and only use strings, integers, boolean, etc. then you may not need a model.

The actual api-docs URL is a Resource Listing JSON that describes the Swagger resources that are available, where they live, and how to use them. If you type the api-docs URL into your browser, you will see the resource listing.

There are three ways to create a Swagger Specification, depending on which server you are using:
- **Codegen:** This is the traditional way of creating a Swagger Specification. The [swagger codegen](https://github.com/wordnik/swagger-codegen) converts annotations in your code into the Swagger Specification. For an example of the annotations see [this code](https://github.com/wordnik/swagger-codegen/blob/master/samples/server-generator/java-jaxrs/output/src/main/java/com/wordnik/api/PetApi.java).
- **Automatically:** Some servers, such as [swagger-node-express](https://npmjs.org/package/swagger-node-express) and [swagger-play](https://github.com/wordnik/swagger-core/tree/master/modules/swagger-play2), will create both your REST APIs and your Swagger Specification for you at the same time.
- **By Hand:** And finally, you can always create your Swagger specification by writing the JSON hand. After you write your Swagger specification (or if you get a Swagger specification from a different server) you can use one of the Server generators to create your code, such as the [node.js server generator](https://github.com/wordnik/swagger-codegen/tree/master/samples/server-generator/node)


#### Codegen Swagger Specification Creation
The Swagger codegen can be found on Github: https://github.com/wordnik/swagger-codegen

#### Automatic Swagger Specification Creation

#### Comments Swagger Specification Creation
#### Swagger Specification Creation By Hand
Get a text editor and get going... best of luck!

## Swagger Specification Overview
#### Resource File
Information about resource listings can be found [here](https://github.com/wordnik/swagger-core/wiki/Resource-Listing).

#### API File
Information about API declarations can be found [here](https://github.com/wordnik/swagger-core/wiki/API-Declaration).
#### Model