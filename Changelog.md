## Swagger-Core version changelog

### v1.3.10 - Sep-21 2014
- Fixed broken artifact for swagger-play2 for scala 2.10 and 2.11

### v1.3.9 - Sep-18 2014
- Updated to swagger-ui 2.0.24 (in the samples)
- [#667](https://github.com/wordnik/swagger-core/issues/667) fixed authorizations in 1.3.8
- [#633](https://github.com/wordnik/swagger-core/issues/633) added hidden=true support for @Api operations
- [#663](https://github.com/wordnik/swagger-core/issues/663) added scanning of interfaces with @Inherited annotation
- Support for scala 2.9.x has been dropped, and the Play module support is 2.3.x and greater.

### v1.3.8
This release had a major bug in it, please refrain from using it.

### v1.3.7 - Jul-14 2014
- Removed hashes for authorization fields which are not oAuth (no ticket)
- Added parameter descriptions in swagger-servlet module ([#604](https://github.com/wordnik/swagger-core/issues/604))
- Models not merged when a resource with same path is in multiple files: ([#610](https://github.com/wordnik/swagger-core/issues/610))
- Updated swagger-ui in samples to 2.0.18

### v1.3.6 - Jun-25 2014
- Simplified mechanism to override datatypes in the model introspection process ([#588](https://github.com/wordnik/swagger-core/issues/588), [#481](https://github.com/wordnik/swagger-core/issues/481))
- Extracting models from multiple classes in the same documentation path is now supported ([#560](https://github.com/wordnik/swagger-core/issues/560))
- Overriding model as a primitive ([#573](https://github.com/wordnik/swagger-core/issues/573))

### v1.3.5 - Apr-28 2014
- Added @BeanParam support for Jersey 2.x.  This community contribution allows better modularization of your app by using a @BeanParam annotation.  Swagger will now scan annotated properties in your POJO.  [#446](https://github.com/wordnik/swagger-core/issues/446), [#537](https://github.com/wordnik/swagger-core/issues/537), [#532](https://github.com/wordnik/swagger-core/issues/532)
- Better Date support for XMLGregorianCalendar,  LocalDateTime [#524](https://github.com/wordnik/swagger-core/issues/524), [#525](https://github.com/wordnik/swagger-core/issues/525), [#509](https://github.com/wordnik/swagger-core/issues/509)
- Updated Jersey2 sample.  There are subtle but important differences in how jersey2 and Swagger interact, you can see a fully functional integration here: https://github.com/wordnik/swagger-core/tree/master/samples/java-jersey2

### v1.3.4 - Apr-2 2014

- You can now integrate a JAX-RS project without any custom annotations ([#489](https://github.com/wordnik/swagger-core/issues/489)).  While not all swagger niceties are supported, you can see an example here: https://github.com/wordnik/swagger-core/tree/master/samples/java-jaxrs-no-annotations
- Better support for Joda DateTime, XMLGregorianCalendar models. They are automatically converted into JSON Schema formats ([#509](https://github.com/wordnik/swagger-core/issues/509), [#439](https://github.com/wordnik/swagger-core/issues/439)) 
- Automatic handling of java.util.Collection.
- Updated Reflections package ([#465](https://github.com/wordnik/swagger-core/issues/465)).
- Easier integration for Jersey 2.x. See the sample: https://github.com/wordnik/swagger-core/tree/master/samples/java-jersey2
- API Operations defined in multiple files are automatically merged ([#501](https://github.com/wordnik/swagger-core/issues/501)).
- More Jackson support ([#441](https://github.com/wordnik/swagger-core/issues/441), [#461](https://github.com/wordnik/swagger-core/issues/461), [#486](https://github.com/wordnik/swagger-core/issues/486)).
- Fixed missing allowMultiple request param ([#478](https://github.com/wordnik/swagger-core/issues/478)).
- Added `hidden` annotations to allow removing operations and properties without a custom filter ([#488](https://github.com/wordnik/swagger-core/issues/488), [#475](https://github.com/wordnik/swagger-core/issues/475), [#167](https://github.com/wordnik/swagger-core/issues/167)).
- Unified versions between annotations, core, play support ([#450](https://github.com/wordnik/swagger-core/issues/450)).

### v1.3.3 - skipped

### v1.3.2 Jan-16 2014

- Added support for Jersey2
- Simplified configuration for non web.xml
- Fixes for Servlet integration 
- Added ApiInfo support for Play2
- Model annotation fixes
- Updates to OAuth2 support annotations to include scopes
- OAuth2 server support
- Fixes for custom model processor support

### v1.3.0 Aug-12 2013

- Added JSON-schema draft-4 support for models
- Support for polymorphic models with discriminiators, subtypes
- Added apiInfo so top-level API information can be annotated in both swagger-ui and elsewhere
- Released swagger-spec 1.2
- Pluggable readers for scanning classes, model introspection, config reading
- Simplified overriding of model introspection
- Support for consumes, produces, protocols, authentications
- oAuth support in spec
- Support for raw servlet integration
- Resource, operation, model property ordering
- Polymorphic model support

### v1.2.5 Jun-19 2013

- Fixes for generic objects

### v1.2.4 Jun-5 2013

- Fixed `@Api` paths with slashes
- Added support for model detection with deep recursion (#176)
- i18n support (#190)
- Master is now `stable`

### v1.2.3 Apr-24 2013

- Updated to Jackson 2.1.4
- Fix for generics as input/output values
- Add manual model definitions

### v1.2.2 Apr-8 2013

### v1.2.0 Nov-27 2012

- Updated listing path to /api-docs.json
- Moved swagger-play2 and swagger-play2-utils to maven central

### v1.1.0 Aug-22 2012

- Spec [clarifications](https://github.com/wordnik/swagger-core/wiki/Changelog)
- Updated to Jackson 2.0.x, added sample with [scala case classes](https://github.com/wordnik/swagger-core/tree/master/samples/scala-jaxrs-jackson2)
- Play 2.0.2 supported
- Pluggable model processor, added configurable package introspection
- Feature parity between JAX-RS & Play 2, java, scala
- Made .{format} configurable
- Added support for alternate resource listing, examples in scala
- Moved swagger spec to github [wiki](https://github.com/wordnik/swagger-core/wiki)


### v1.01 Jan-31 2012

- Transitioned build to Maven</li>
- Removed jaxrs dependencies from swagger-core</li>
- Moved sample server implementations into /samples directory</li>
- Added support for alternate API listing paths</li>
- Made .{format} optional</li>
- Added crazy scala versioning into artifact names (Scala 2.8.1 => ${artifact}_2.8.1)
- Added test and integration test through default maven integration-test lifecycle</li>

### v1.0 Aug-10 2011
- Initial release of swagger</li>

Pre-release versions will be suffixed with SNAPSHOT and RC appropriately.  If you want the
release version, please grab it by tag (i.e. v1.0 for the release)