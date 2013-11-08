==========

Swagger uses reflection to understand the models that are required as either input parameters or model attributes.  For instance, a `User` object might have an `Address` attribute.  Swagger will attempt to introspect the entire object hierarchy so that all aspects of the model can be provided to the consumer.

You can control what the Swagger introspection code sees with Swagger's own `ApiProperty`, using JAXB, or even @JsonIgnore annotations.  When you use 3rd party or even generic Java objects, this can get tricky as you may not want all aspects of the model being represented in the Swagger JSON.  For example, take `java.util.Date`.

By default, the `Date` object will be introspected from public methods, and look something like this:

```json
"Date" : {
  "id" : "Date",
  "properties" : {
    "time" : {
      "type" : "long"
    },
    "minutes" : {
      "type" : "int"
    },
    "seconds" : {
      "type" : "int"
    },
    "hours" : {
      "type" : "int"
    },
    "month" : {
      "type" : "int"
    },
    "year": {
      "type" : "int"
    },
    "timezoneOffset" : {
      "type" : "int"
    },
    "day" : {
      "type" : "int"
    },
    "date" : {
      "type" : "int"
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

### In scala, with swagger-core version 1.3.x:

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

### In java, with swagger-core version 1.3.x:

```java
import com.wordnik.swagger.converter.*;

String jsonString = "{" +
"  \\"id\\": \\"Date\\"," +
"  \\"properties\\": {" +
"    \\"value\\": {" +
"      \\"required\\": true," +
"      \\"description\\": \\"Date in ISO-8601 format\\"," +
"      \\"notes\\": \\"Add any notes you like here\\"," +
"      \\"type\\": \\"string\\"," +
"      \\"format\\": \\"date-time\\"" +
"    }" +
"  }" +
"}";
ModelConverter converter = new OverrideConverter();
converter.add("java.util.Date", jsonString);

ModelConverters.addConverter(converter, true);
```

### Excluding fields with a Custom Model Converter
If you want to exclude certain field types from your models, you can do so with a custom model converter.  For example, perhaps you have a model with a property type `EntityBeanIntercept` from your model but you don't want to override the entire model--you can do so as follows:

```scala
class CustomConverter extends SwaggerSchemaConverter {
  override def skippedClasses: Set[String] = Set("com.avaje.ebean.bean.EntityBeanIntercept")
}
```

The fully-qualified types listed in the `skippedClasses` will neither be shown for the model, nor expanded into the model schema.  See the unit test below, which excludes the property `bar` of type `Bar` from the model `Foo`:

```scala
class CustomConverterTest extends FlatSpec with ShouldMatchers {
  it should "ignore properties with type Bar" in {
    // add the custom converter
    ModelConverters.addConverter(new CustomConverter, true)

    // make sure the field bar: converter.Bar is not present
    ModelConverters.read(classOf[Foo]) match {
      case Some(model) => model.properties.get("bar") should be (None)
      case _ => fail("didn't read anything")
    }
  }
}

class Foo {
  @BeanProperty var bar:Bar = null
  @BeanProperty var title:String = null
}

class Bar {
  @BeanProperty var foo:String = null
}
```