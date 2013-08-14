==========

1.2.  This version is in release-candidate status and contains high-level changes.  See the [1.2 transition guide](1.2-transition) for information on transitioning both your spec and server integration.

* Support for authorization description

* Additional metadata about the API, including developer, contact, TOS, license

* Added the following api-level descriptions, which can be overridden at the operation level:

  - consumes.  Describes the input formats supported when receiving content

  - produces.  The output formats that the resource can produce

  - protocols.  The protocols supported by the resource (i.e. `http`, `https`, `ws`, `wss`)

  - authorizations.  The required authorizations for a resource

* The `.{format}` in the path is now optional and off by default

* Support for JAX-RS sub resources

* Model inheritence, base-classes, and descriminators

1.1.  This version contains mostly clarifications to ambiguous portions of the spec, including:

* Clarify supported primitives, case normalization of models.

* Case normalization of container types

* No longer assume `resources.json` as resource declaration.  Codegen and UI must point directly to the resource listing document

* Return type of "ok" renamed to "void"

* Addition of `.{format}` to API Listing document's `path`

1.0. Initial release