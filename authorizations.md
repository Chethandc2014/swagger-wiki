Authorizations
==========

The purpose of the authorizations section is to prescribe how clients should connect to the API.  There are two supported mechanisms:

* static header (i.e. API Key).  A statically-defined header or query param is passed to the API.  The name and transfer method are configured by the site provider.

* oAuth 2.0.  The goal with describing the oAuth2 structure is to allow programmatic discovery and connectivity given a known set of endpoints for performing the oauth dance.

In both cases, each authorization scheme is named for reference elsewhere in the spec.  For example, it is allowed to define both an api key and multiple oauth schemes for a single API--different operations may require different authorization schemes to be satisfied before allowing access.  So, for example:

```json
  "authorizations": {
    "local-oauth": {
      "type": "oauth2",
      "scopes": [
        "PUBLIC"
      ],
      "grantTypes": {
        "implicit": {
          "loginEndpoint": {
            "url": "http://sample.com/oauth/dialog"
          },
          "tokenName": "access_code"
        },
        "authorization_code": {
          "tokenRequestEndpoint": {
            "url": "http://sample.com/oauth/requestToken",
            "clientIdName": "client_id",
            "clientSecretName": "client_secret"
          },
          "tokenEndpoint": {
            "url": "http://sample.com/oauth/token",
            "tokenName": "access_code"
          }
        }
      }
    },
    "apiKey": {
      "type": "apiKey",
      "passAs": "header"
    }
  }
```

Here, there is a scheme named `local-oauth` of type `oauth2`.  It provides both `implicit` and `authorization_code` flows.

For the `implicit` flow, a token named `access_code` will be returned after successfully logging in to the `http://sample.com/oauth/dialog` UI.

For the `authorization_code` flow, tokens are requested at the indicated endpoint with the `client_id` and `client_secret` names.  The token itself is received from the `tokenEndpoint` url with name `access_code`.

The goal of specifying this information is to avoid having your developers hunt through wiki and web pages looking for what params to pass, and to where.

The scheme named `apiKey` requires a parameter named `apiKey` to be passed as a header.  This will satisfy authorization requirements wherever `apiKey` is defined in the system.