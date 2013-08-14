==========

Swagger uses reflection to understand the models that are required as either input parameters or model attributes.  For instance, a `User` object might have an `Address` attribute.  Swagger will attempt to introspect the entire object hierarchy so that all aspects of the model can be provided to the consumer.

You can control what the Swagger introspection code sees with Swagger's own `ApiProperty`, using JAXB, or even @JsonIgnore annotations.  When you use 3rd party or even generic Java objects, this can get tricky as you may not want all aspects of the model being represented in the Swagger JSON.  For example, take `java.util.Date`.

By default, the `Date` object will be introspected from public methods, and look something like this:

```json
"Date": {
  "id": "Date",
  "properties": {
    "time": {
      "type": "long"
    },
    "minutes": {
      "type": "int"
    },
    "seconds": {
      "type": "int"
    },
    "hours": {
      "type": "int"
    },
    "month": {
      "type": "int"
    },
    "year": {
      "type": "int"
    },
    "timezoneOffset": {
      "type": "int"
    },
    "day": {
      "type": "int"
    },
    "date": {
      "type": "int"
    }
  }
}
```

Now you probably don't want the client to build it's own `Date` object from this description.  If you, for instance, have configured your API to send the date in `ISO-8601` format, the consumer probably wants to know that the date is coming as a `string` format.  Thus, you really want your `Date` to serialize like such:

```json
"Date": {
  "id": "Date",
  "properties": {
    "value": {
      "required": true,
      "description": "Date in ISO-8601 format",
      "notes": "Add any notes you like here",
      "type": "string"
    }
  }
}
```

This can be done by telling the model introspector to avoid reflecting over `java.util.Date` and instead to use a prescribed model representation:

### With swagger-core version 1.3.0:

```scala
import com.wordnik.swagger.converter.{ ModelConverters, OverrideConverter }

val jsonString = """
{
  "id": "Date",
  "properties": {
    "value": {
      "required": true,
      "description": "Date in ISO-8601 format",
      "notes": "Add any notes you like here",
      "type": "string",
      "format": "date-time"
    }
  }
}
"""
val converter = new OverrideConverter
converter.add("java.util.Date", jsonString)

ModelConverters.addConverter(converter, true)

```

This needs to be fired on startup of the app, typically in a bootstrap servlet.  Now whenever a `java.util.Date` is encountered by Swagger, it will use this definition.