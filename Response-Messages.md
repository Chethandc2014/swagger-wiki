==========

The API Declaration should describe how the different `responseMessages` map to the
business logic of the API.  For instance, if a 404 is returned, it may be due to a variety
of reasons--invalid resource, object cannot be found, invalid parameter, etc.  While
the HTTP response codes can be interpreted by the consumer correctly, often they are
ambiguous and can benefit from some documentation.

The `responseMessage` object contains just two fields, as shown below:

```json
"responseMessages":[
  {
    "code": 200,
    "message": "Ok",
    "responseModel": "Pet"
  },
  {
    "code": 404,
    "message": "The pet cannot be found"
  }
],

```

`code`.  This is the `HTTP` response code which causes the error condition

`message`.  This is a text explanation of what will cause the error condition 

`responseModel`.  An optional response class to accompany the response code