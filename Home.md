What is Swagger?
==========

Swagger is a specification and complete framework implementation for describing, producing, 
consuming, and visualizing RESTful web services.  The overarching goal of Swagger is to 
enable client and documentation systems to update at the same pace as the server.  
The documentation of methods, parameters and models can be tightly integrated into the 
server code, allowing APIs to always stay in sync.

#### Who is responsible for Swagger?
Both the specification and framework implementation are initiatives from Wordnik.  Swagger 
was developed for Wordnik's own use during the development of [http://developer.wordnik.com/docs](http://developer.wordnik.com/docs) 
and the underlying [http://api.wordnik.com/v4](http://api.wordnik.com/v4/resources.json).  Swagger development began in early 2010—the 
framework being released is currently used by Wordnik’s APIs, which power both internal and external API clients.

#### Why is Swagger useful?
The Swagger framework simultaneously addresses server, client, and documentation/sandbox 
needs for REST APIs.  As a specification, it is language-agnostic.  It also provides a 
long runway into new technologies and protocols beyond HTTP.

With Swagger's declarative resource specification, clients can understand and consume services 
without knowledge of server implementation or access to the server code.  The Swagger UI 
framework allows both developers and non-developers to interact with the API in 
a sandbox UI that gives clear insight into how the API responds to parameters and options.
Swagger happily speaks both JSON and XML, with additional formats in the works.

#### How can I make my API Swagger?
There are a number of different ways.  If you're impatient you can skip directly to the [tutorials](wiki/tutorial)

You can use the [swagger-core](http://github.com/wordnik/swagger-core) framework,
which gives you everything you need to implement a Swagger server using node.js, Java or scala 
using several different REST frameworks.

You can use or develop a framework for automatically generating the Swagger specification.
Some known server implementations include:

[node.js](https://github.com/wordnik/swagger-node-express) with express framework

[java/scala JAX-RS](https://github.com/wordnik/swagger-core/tree/master/modules/swagger-jaxrs)

[scala play 1.4](https://github.com/wordnik/swagger-core/tree/master/modules/swagger-play)

[scala play2](https://github.com/ayush/swagger-play2)

[scalatra](http://www.scalatra.org/) via [swagger-support](https://github.com/scalatra/scalatra/tree/master/swagger/src/main/scala/org/scalatra/swagger)

[fubumvc-swagger](https://github.com/KevM/fubumvc-swagger) .net Fubumvc

[Swagger.net](https://github.com/miketrionfo/Swagger.Net) .net integration from Mike Trionfo

[Swagger-PHP](http://packagist.org/packages/zircote/swagger-php) PHP Composer

[Symfony-2](https://github.com/nelmio/NelmioApiDocBundle) Bundle

[Restler](https://github.com/Luracast/Restler) PHP framework, swagger support planned in 3.0

You can generate or manually write simple, static JSON documents which describe your existing 
API.  That means you can benefit from the [http://github.com/wordnik/swagger-ui](swagger-ui) and [http://github.com/wordnik/swagger-codegen](swagger-codegen) without
doing anything to your server other creating some files.  They don't even need to exist on the same
server.  One example of a swagger-spec generator is here:

[https://github.com/solso/source2swagger](https://github.com/solso/source2swagger) (ruby)

### Anatomy of the Spec

The specification is in two parts--the Resource Listing and the API Description.  The
Resource Listing contains an inventory of APIs available on the server as well as paths
to the description of the APIs.

See [Resource Listing](https://github.com/wordnik/swagger-core/wiki/Resource-Listing) and [API Declaration](https://github.com/wordnik/swagger-core/wiki/API-Declaration)