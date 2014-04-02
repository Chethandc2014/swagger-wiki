The product info data 

```json

"pinfo:<product_id>":{
    "Service":"AddProduct",
    "Name":"ProductName",
    "Title":"ProductTitle",
    "Price":"3200",
    "Images": {
		"default":"image_url",
		"portrait":["image_url1","image_url2","image_url3",…],
		"landscape":["image_url1","image_url2","image_url3",…]
	},
	"Metadata":{
		"SomeKey":"SomeValue",
		"AnotherKey":"AnotherValue",
		……
	},
	"Status":"Ready/OutOfStock/Waiting",
	"Properties":{
		"ma":[
			{
				"PropertyId":4,
				"PropertyValue":"NYCQ"
			}
		],
		"mb":[
			{
				"PropertyId":5,
				"PropertyValue":"Slow"
			},
			{
				"PropertyId":6,
				"PropertyValue":"Rock"
			}
		]
	}
}

```

--------

# Message of the Day API
A simple [MOTD](http://en.wikipedia.org/wiki/Motd_(Unix)) API.

# Message [/messages/{id}]
This resource represents one particular message identified by its *id*.

## Retrieve Message [GET]
Retrieve a message by its *id*.

+ Response 200 (text/plain)

        Hello World!

## Delete Message [DELETE]
Delete a message. **Warning:** This action **permanently** removes the message from the database.

+ Response 204







