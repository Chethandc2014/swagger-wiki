This is the roadmap for the Swagger Specification.

Any changes in the specification for the major version shall be non-breaking to current consumers.  Examples include be renaming an attribute, changing types, etc.

A minor version shall be introduced when major functionality is provided

## Features
**1.0 Protocol declaration**.  The specification should provide information on what protocols are supported and/or required for each method.  To keep the specification from becoming too verbose, they should be declared at the top level (api-docs.json) and overridden at an API Listing, then operation fashion.  Protocols include http, https, ws, wss.

**2.0 Content type declaration**.  In the same, inheritance fashion as with 1.0, the content type should be defined from both the accept & produce stand point.

**3.0 Authorization**.  Describing the requirements to access the API through API Key, oAuth, etc. will be described in the specification, and will be inherited from resource listing to api declaration to operation.

## Milestones