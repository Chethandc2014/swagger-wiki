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

- [node.js](https://github.com/wordnik/swagger-node-express) with express framework

- [Java/Scala JAX-RS](https://github.com/wordnik/swagger-core/tree/master/modules/swagger-jaxrs)

- [RESTEasy](http://www.jboss.org/resteasy)

- [Grails 2](http://grails.org)

- [Scala Play 1.4](https://github.com/wordnik/swagger-core/tree/master/modules/swagger-play)

- [Scala Play 2](https://github.com/wordnik/swagger-core/tree/master/modules/swagger-play2)

- [Scalatra](http://www.scalatra.org/) via [swagger-support](https://github.com/scalatra/scalatra/tree/master/swagger/src/main/scala/org/scalatra/swagger), see [Swagger Guide](http://www.scalatra.org/2.2/guides/swagger.html)

- [Spring MVC](https://github.com/martypitt/swagger-springmvc)

- [ServiceStack](https://github.com/ServiceStack/ServiceStack) .net/MONO

- [fubumvc-swagger](https://github.com/KevM/fubumvc-swagger) .net Fubumvc

- [Swagger.net](https://github.com/miketrionfo/Swagger.Net) .net integration from Mike Trionfo

- [Swagger-PHP](http://packagist.org/packages/zircote/swagger-php) PHP Composer

- [Symfony 2](https://github.com/nelmio/NelmioApiDocBundle) Bundle

- [Restler](https://github.com/Luracast/Restler) PHP framework, swagger support in 3.0

- [grape-swagger](https://github.com/tim-vandecasteele/grape-swagger) for Ruby

- [django-tastypie-swagger](https://github.com/minism/django-tastypie-swagger) for Python/Django

- [Swaggerator](https://github.com/myfreeweb/swaggerator) for Clojure

You can generate or manually write simple, static JSON documents which describe your existing 
API.  That means you can benefit from the [swagger-ui](http://github.com/wordnik/swagger-ui) and [swagger-codegen](http://github.com/wordnik/swagger-codegen) without
doing anything to your server other creating some files.  They don't even need to exist on the same
server.  Some known implementations include:

- [Ruby](https://github.com/solso/source2swagger)

- [JAX-RS & Javadoc] (https://github.com/ryankennedy/swagger-jaxrs-doclet)

Besides, you can generate your custom API documents without starting any servers, just during **build phase** by using a simple maven plugin:

- [Swagger maven plugin](https://github.com/kongchen/swagger-maven-plugin)

### Anatomy of the Spec

The specification is in two parts--the Resource Listing and the API Declaration.  The
Resource Listing contains an inventory of APIs available on the server as well as paths
to the description of the APIs.

See [Resource Listing](https://github.com/wordnik/swagger-core/wiki/Resource-Listing) and [API Declaration](https://github.com/wordnik/swagger-core/wiki/API-Declaration)