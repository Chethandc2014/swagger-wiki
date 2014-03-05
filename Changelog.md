==========
## Swagger-Core version changelog

### v1.3.2 Jan-16 2014

<li>- Added support for Jersey2

<li>- Simplified configuration for non web.xml

<li>- Fixes for Servlet integration 

<li>- Added ApiInfo support for Play2

<li>- Model annotation fixes

<li>- Updates to OAuth2 support annotations to include scopes

<li>- OAuth2 server support

<li>- Fixes for custom model processor support

### v1.3.0 Aug-12 2013

<li>- Added JSON-schema draft-4 support for models

<li>- Support for polymorphic models with discriminiators, subtypes

<li>- Added apiInfo so top-level API information can be annotated in both swagger-ui and elsewhere

<li>- Released swagger-spec 1.2

<li>- Pluggable readers for scanning classes, model introspection, config reading

<li>- Simplified overriding of model introspection

<li>- Support for consumes, produces, protocols, authentications

<li>- oAuth support in spec

<li>- Support for raw servlet integration

<li>- Resource, operation, model property ordering

<li>- Polymorphic model support

### v1.2.5 Jun-19 2013
<li>- Fixes for generic objects

### v1.2.4 Jun-5 2013
<li>- Fixed `@Api` paths with slashes

<li>- Added support for model detection with deep recursion (#176)

<li>- i18n support (#190)

<li>- Master is now `stable`

### v1.2.3 Apr-24 2013

<li>- Updated to Jackson 2.1.4

<li>- Fix for generics as input/output values

<li>- Add manual model definitions

### v1.2.2 Apr-8 2013

### v1.2.0 Nov-27 2012

<li>- Updated listing path to /api-docs.json

<li>- Moved swagger-play2 and swagger-play2-utils to maven central

### v1.1.0 Aug-22 2012

<li>- Spec [clarifications](https://github.com/wordnik/swagger-core/wiki/Changelog)

<li>- Updated to Jackson 2.0.x, added sample with [scala case classes](https://github.com/wordnik/swagger-core/tree/master/samples/scala-jaxrs-jackson2)

<li>- Play 2.0.2 supported

<li>- Pluggable model processor, added configurable package introspection

<li>- Feature parity between JAX-RS & Play 2, java, scala

<li>- Made .{format} configurable

<li>- Added support for alternate resource listing, examples in scala

<li>- Moved swagger spec to github [wiki](https://github.com/wordnik/swagger-core/wiki)


### v1.01 Jan-31 2012

<li>- Transitioned build to Maven</li>

<li>- Removed jaxrs dependencies from swagger-core</li>

<li>- Moved sample server implementations into /samples directory</li>

<li>- Added support for alternate API listing paths</li>

<li>- Made .{format} optional</li>

<li>- Added crazy scala versioning into artifact names (Scala 2.8.1 => ${artifact}_2.8.1)

<li>- Added test and integration test through default maven integration-test lifecycle</li>

### v1.0 Aug-10 2011

<li>- Initial release of swagger</li>

Pre-release versions will be suffixed with SNAPSHOT and RC appropriately.  If you want the
release version, please grab it by tag (i.e. v1.0 for the release)