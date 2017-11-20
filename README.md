jrgen (json-rpc-generator) generates docs, tests, clients and servers for json-rpc apis.  

Generated example files can be found in the [examples directory](https://github.com/mzernetsch/jrgen/tree/master/examples).

### docs
- [html](https://rawgit.com/mzernetsch/jrgen/master/examples/docs/html/ExampleAPI.html)
- [md](https://rawgit.com/mzernetsch/jrgen/master/examples/docs/md/ExampleAPI.md)
- [gitbook](https://github.com/mzernetsch/jrgen/tree/master/examples/docs/gitbook)

### test
- [jasmine](https://github.com/mzernetsch/jrgen/tree/master/examples/test/jasmine)

### client
- [es6](https://github.com/mzernetsch/jrgen/blob/master/examples/client/es6/ExampleAPIClient.js)
- [ts](https://github.com/mzernetsch/jrgen/blob/master/examples/client/ts/ExampleAPIClient.ts)

### server
- [nodejs](https://github.com/mzernetsch/jrgen/blob/master/examples/server/nodejs/ExampleAPIServer.js)

Installation
------------
```bash
npm install -g jrgen
```

Usage
-----
jrgen is divided in 4 sections 'docs', 'test', 'client' and 'server'. Call `jrgen -h` or `jrgen [section] -h` for usage information.

### Examples
Create html documentation from 'API.schema.json'.
```bash
jrgen docs ~/API.schema.json
```

Create jasmine unit tests from 'API.schema.json'.
```bash
jrgen test -f jasmine ~/API.schema.json
```

Create a js client from 'API.schema.json' and write all generated files into the ./client subdirectory.
```bash
jrgen client -o ./client ~/API.schema.json
```

Create a nodejs server from a combination of 'API1.schema.json' and 'API2.schema.json'. The generated server will handle the api calls of both schemes. A schema will overwrite all properties of its preceeding schemes.
```bash
jrgen server ~/API1.schema.json ~/API2.schema.json
```

Specification
-------------
jrgen uses special [specification files](https://github.com/mzernetsch/jrgen/blob/master/examples/ExampleAPI.schema.json) which describe all available methods, the parameters and the expected result or error responses of an api. A specification file contains valid json and mostly consists of [JSON-Schema](https://spacetelescope.github.io/understanding-json-schema/). A JSON-Schema describing a jrgen specification can be found [here](https://github.com/mzernetsch/jrgen-schema/blob/master/jrgen.schema.json).   
If the api is really large you may consider splitting the specification into multiple files and create references using [JSON-Pointer](https://spacetelescope.github.io/understanding-json-schema/structuring.html#reuse).

```js
{
	"$schema": "https://rawgit.com/mzernetsch/jrgen-schema/master/jrgen.schema.json",	//Link to the schema. Used for validation and autocompletion in certain editors.
	"jrgen":"1.0",		//jrgen version. Currently always "1.0".
	"jsonrpc":"2.0",	//jsonrpc version. Currently always "2.0".

	"info":{
		"title":"ExampleAPI",	//Name of your api.
		"description":"This api handles various rpc requests.",	//Description or usage information about your api.
		"version":"1.0"	//Current version of your api
	},

	"definitions":{	//You can define global types and reference them from anywhere using a "$ref" property
		"session":{
			"type":"object",
			"properties":{
				"session_token":{
					"description":"Bearer token of the created session.",
					"default":"123456890",

					"type":"string",
					"minLength":1
				}
			},

			"required":["session_token"]
		}
	},

	"methods":{	//All methods of the api are specified within this object.
		"Session.Login":{	//The key of the property equals to the name of the method.
			"summary":"Creates a new session.",	//Short summary of what the method does.
			"description":"Authenticates the user using the provided credentials and creates a new session.",	//Longer description of what the method does.

			"tags":["Session"],	//Tags for grouping similar methods.

			"params":{	//json-schema of the params object within a json-rpc request. Can be omitted if not used.
				"type":"object",
				"properties":{
					"name":{
						"description":"Name of the user to create a session for.",	//You can provide a description for every property.
						"default":"admin",	//You should provide a valid default value for each non-object and non-array property. These provided default values will be used to generate example requests and responses.

						"type":"string",
						"minLength":1
					},
					"password":{
						"description":"Password of the user to create a session for.",
						"default":"123456",

						"type":"string",
						"minLength":1
					}
				},

				"required":["name", "password"]
			},

			"result":{	//json-schema of the result object within a json-rpc response. Can be omitted if not used.
				"$ref":"#/definitions/session" //Reference to a global type
			},

			"errors":[	//Possible errors in a json-rpc response. Can be omitted if not used.
				{
					"description":"The provided credentials are invalid.",

					"code":1,	//code is always an integer.
					"message":"InvalidCredentials"	//message is always a string.
					"data":{	//json-schema of the data object within a json-rpc error. Can be omitted if not used.

					}
				}
			]
		}
	}
}
```
