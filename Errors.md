Error Declaration
==========

The API Declaration should describe how the different `errorResponses` map to the
business logic of the API.  For instance, if a 404 is returned, it may be due to a variety
of reasons--invalid resource, object cannot be found, invalid parameter, etc.  While
the HTTP response codes can be interpreted by the consumer correctly, often they are
ambiguous and can benefit from some documentation.

The `errorResponse` object contains just two fields, as shown below:

```json
"errorResponses":[
  {
    "code": 400,
    "reason": "Raised if a user supplies an invalid username format"
  },
  {
    "code": 404,
    "reason": "The user cannot be found"
  }
],

```

`code`.  This is the `HTTP` response code which causes the error condition

`reason`.  This is a text explanation of what will cause the error condition 