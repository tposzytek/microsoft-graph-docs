---
title: "Javascript client library overview"
description: "Provides an overview of the Javascript client library."
localization_priority: Normal
author: sdk-contributors
---

# Microsoft Graph Javascript client library overview

## Create a client instance


### 1. Create With ClientOptions [Recommended]

You can pass in the `authProvider` or `middleware` chain in `ClientOptions` to instantiate a Client object.

#### Option A. Default middleware chain

Pass an instance of a class which implements `AuthenticationProvider` interface as `authProvider` in `ClientOptions`, which will instantiate the Client with default set of middleware chain.

This library is shipped with an authentication provider named `ImplicitMSALAuthenticationProvider`. The `ImplicitMSALAuthenticationProvider` depends on an authentication library [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) which is not shipped along with the library. You have to include msal.js to use the `ImplicitMSALAuthenticationProvider`.

```typescript
// Instantiating Client with ImplicitMSALAuthenticationProvider

// An Optional options for initializing the MSAL @see https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options
const msalConfig = {
	auth: {
		clientId: <CLIENT_ID> // Client Id of the registered application
	},
};

// Important Note: This library implements loginPopup and acquireTokenPopup flow, remember this while initializing the msal
// Initialize the MSAL @see https://github.com/AzureAD/microsoft-authentication-library-for-js#1-instantiate-the-useragentapplication
const msalApplication = new UserAgentApplication(msalConfig);
const options = new MicrosoftGraph.MSALAuthenticationProviderOptions(<SCOPES>); // An array of graph scopes
let clientOptions: ClientOptions = {
    authProvider: new ImplicitMSALAuthenticationProvider(msalApplication, options)
};
const client = Client.initWithMiddleware(clientOptions);
```

You have to implement the `AuthenticationProvider` interface and pass in the instance of it as `authProvider` in `ClientOptions` if you want to use another authentication library.

```typescript
let clientOptions: ClientOptions = {
	// MyCustomAuthenticationProvider is the user's own authentication provider implementing AuthenticationProvider interface
	authProvider: new MyCustomAuthenticationProvider(),
};
const client = Client.initWithMiddleware(clientOptions);
```

Read [custom authentication provider](#custom-authentication-provider) for more information.

#### Option B. Custom middleware chain

Want to have complete control over the request and the response objects, one can provide his own chain of middleware. Have to pass first middleware in the chain as middleware in `ClientOptions`.

```typescript
let clientOptions: ClientOptions = {
	// MyFirstMiddleware is the first middleware in my custom middleware chain
	middleware: new MyFirstMiddleware(),
};
const client = Client.initWithMiddleware(clientOptions);
```

<!-- TODO: Add section for custom middleware chain -->
Read [custom middleware chain](#custom-middleware-chain) for more information.

### 2. Create With Options

Pass an `AuthProvider` function in an `Options` object when you initialize the client. You can use this to provide your own implementation for getting and refreshing access tokens. A callback must be passed into this `AuthProvider` function and an  access token or error object needs to be passed in to that callback.

```typescript
const authProvider: AuthProvider = (callback: AuthProviderCallback) => {
	// Your logic for getting and refreshing accessToken

	// Error should be passed in case of error while authenticating
	// accessToken should be passed upon successful authentication
	callback(error, accessToken);
};
let options: Options = {
	authProvider,
};
const client = Client.init(options);
```

Read [custom middleware chain](#custom-middleware-chain) for more information.

## Custom authentication provider

You may want to use an authentication library other than the preferred msal.js library or you may want to implementation authentication yourself. This section shows how you can do that. You will implement an `AuthenticationProvider` interface.

```typescript

import { AuthenticationProvider } from "@microsoft/microsoft-graph-client";

class MyCustomAuthenticationProvider implements AuthenticationProvider {
	/**
	 * This method will get called before every request to the msgraph server
	 * This should return a Promise that resolves to an accessToken (in case of success) or rejects with error (in case of failure)
	 * Basically this method will contain the implementation for getting and refreshing accessTokens
	 */
	public async getAccessToken(): Promise<any> {}
}
```

## Calling pattern

All calls to Microsoft Graph are chained together starting with **.api()**, then chain query parameters and end with an action.

### Path supports the following formats

-   `me`
-   `/me`
-   `https://graph.microsoft.com/v1.0/me`
-   `https://graph.microsoft.com/beta/me`
-   `me/events?$filter=startswith(subject, "Adventure")`

### Promise based calling

Getting user details with `async`/`await`,

```typescript
try {
	let res = await client.api("/me").get();
	console.log(res);
} catch (error) {
	throw error;
}
```

Getting user details with `then`/`catch`,

```typescript
client
	.api("/me")
	.get()
	.then((res) => {
		console.log(res);
	})
	.catch((err) => {
		console.log(err);
	});
```

### Callback based calling

Getting user details by passing `callback`,

```typescript
client.api("/me").get((err, res) => {
	console.log(res);
});
```

## Methods for calling Microsoft Graph

### GET

You can get information from the graph using `.get()`.

Getting user details

```typescript
try {
	let res = await client.api("/me").get();
	console.log(res);
} catch (error) {
	throw error;
}
```

### GETSTREAM

You can download information from the graph using NodeJS streams with `.getStream()`.

Downloading a file from OneDrive

```typescript
const fs = require("fs");
client
	.api("/me/drive/root/children/<FILE_NAME>/content")
	.getStream() //Eg: test.pdf
	.then((stream) => {
		let writeStream = fs.createWriteStream(`../<DOWNLOAD_FILE_NAME>`); // Eg: test.pdf
		stream.pipe(writeStream).on("error", (err) => {
			throw error;
		});
		writeStream.on("finish", () => {
			console.log("Downloaded");
		});
		writeStream.on("error", (err) => {
			throw error;
		});
	})
	.catch((error) => {
		throw error;
	});
```

### POST

You can submit a content to the graph using `.post()`/`.create()`, this takes object as a param that will be sent as content of the request.

Sending an email to the recipients

```typescript
const mail = {
	subject: "Microsoft Graph JavaScript Sample",
	toRecipients: [
		{
			emailAddress: {
				address: "example@example.com",
			},
		},
	],
	body: {
		content: "<h1>MicrosoftGraph JavaScript </h1>Check out https://github.com/microsoftgraph/msgraph-sdk-javascript",
		contentType: "html",
	},
};
try {
	let res = await client.api("/me/sendMail").post({ message: mail });
	console.log(res);
} catch (error) {
	console.error(error);
}
```

### PATCH

You can partially modify the resource in graph using `.patch()`/`.update()`, which takes object as a param that will be sent as content of the request.

Updating the birthday information for a contact

```typescript
let contactId = "<CONTACT_ID_TO_UPDATE>";
let contactInfo = {
	birthday: "1991-07-22",
};
try {
	let res = await client.api(`/me/contacts/${contactId}`).patch(contactInfo);
	console.log(res);
} catch (error) {
	console.error(error);
}
```

### DELETE

You can delete a resource in graph using `.delete()`/`.del()`.

Deleting the file in OneDrive

```typescript
try {
	let res = await client.api(`/me/drive/items/${ONE_DRIVE_FILE_ID_TO_DELETE}`).delete();
	console.log(res);
} catch (error) {
	console.error(error);
}
```

### PUT

You can replace resources in graph using `.put()`.

Updating profile picture from a HTML input form

```typescript
let file; // FileObject retrieved from the HTML input type=file
try {
	let res = await client.api("/me/photo/$value").put(file);
	console.log(res);
} catch (error) {
	throw error;
}
```

### PUTSTREAM

You can replace resources in graph using NodeJS streams with `.putStream()`.

Uploading a file to OneDrive

```typescript
let fs = require("fs");
try {
	let stream = fs.createReadStream("<PATH_TO_LOCAL_FILE>"); // Eg: "./test.jpg"
	let res = await client.api(`/me/drive/root/children/<FILE_NAME>/content`); // Eg: /me/drive/root/children/test.jpg/content
	console.log(res);
} catch (error) {
	throw error;
}
```

## Custom middleware chain

Middleware is a processing chain in middle of the request and response cycle. It has access to context object which contains request, response objects and any other options that are specific to middleware. It has an access to execute next middleware in the chain.

### Implement middleware

Create a custom middleware pipeline by implementing the `Middleware` interface. The following examples demonstrate how to create a custom logging middleware and how to create a custom http request and response handler.

First middleware is passed with the context object containing request, and other middleware specific options. One has to explicitly make calls to execute method of the next middleware with context object once the current middleware work is over.

```typescript
// MyLoggingHandler.ts
import { Middleware } from "@microsoft/microsoft-graph-client";
import { Context } from "@microsoft/microsoft-graph-client";

export class MyLoggingHandler implements Middleware {
	private nextMiddleware: Middleware;

	public async execute(context: Context): Promise<void> {
		try {
			let url: string;
			if (typeof context.request === "string") {
				url = context.request;
			} else {
				url = context.request.url;
			}
			console.log(url);
			return await this.nextMiddleware.execute(context);
		} catch (error) {
			throw error;
		}
	}

	public setNext(next: Middleware): void {
		this.nextMiddleware = next;
	}
}
```

> **Note:** Http message handler should set the response object in the context object.

```typescript
// MyHttpMessageHandler.ts
import { Middleware } from "@microsoft/microsoft-graph-client";

export class MyHttpMessageHandler implements Middleware {
	public async execute(context: Context): Promise<void> {
		try {
			// For more information about context object refer "Context" section below
			let response = await fetch(context.request, context.options);
			// Set the response back in the context
			context.response = response;
			return;
		} catch (error) {
			throw error;
		}
	}
}
```

### Create a middleware chain

You can use custom middleware along with the middleware shipped with the client to create the middleware chain. Create a chained middleware set by linking middleware sequentially by using the `setNext()` method.

The following example uses the AuthenticationHandler shipped with this library, the custom MyLoggingHandler, and the custom MyHttpMessageHandler to create a custom middleware chain here.

> You can provide your own AuthenticationHandler instead of the default `ImplicitMSALAuthenticationProvider`.

```typescript
import { ImplicitMSALAuthenticationProvider } from "@microsoft/microsoft-graph-client";
import { MyLoggingHandler } from "./MyLoggingHandler";
import { MyHttpMessageHandler } from "./MyHttpMessageHandler";

let authProvider = new ImplicitMSALAuthenticationProvider("<CLIENT_ID>", ["user.read"]);
let authenticationHandler = new AuthenticationHandler(authProvider);
let myLoggingHandler = new MyLoggingHandler();
let myHttpMessageHandler = new MyHttpMessageHandler();

// Note: myHttpMessageHandler is the last in the chain so there is no need of setting next middleware for it.
authenticationHandler.setNext(myLoggingHandler);
myLoggingHandler.setNext(myHttpMessageHandler);

// Initialize the client
let clientOptions: ClientOptions = {
	middleware: authenticationHandler,
};
const client = Client.initWithMiddleware(clientOptions);
```

## Passing Options for Middleware

One can pass any middleware specific options or data while initializing the client, this will be available in the `context.middlewareOptions`.

```typescript
let clientOptions: ClientOptions = {
	middleware: authenticationHandler,
	middlewareOptions: {
		loggingPrefix: "MSGraph-Client-Library",
	},
};
```

The above middlewareOptions object will be available in the context object that is being passed to the execute method of a middleware.

```typescript
// MyLoggingHandler.ts
import { Middleware } from "@microsoft/microsoft-graph-client";
import { Context } from "@microsoft/microsoft-graph-client";

export class MyLoggingHandler implements Middleware {
	private nextMiddleware: Middleware;

	public async execute(context: Context): Promise<void> {
		try {
			let url: string;
			if (typeof context.request === "string") {
				url = context.request;
			} else {
				url = context.request.url;
			}
			if (context.middlewareOptions !== undefined && context.middlewareOptions.loggingPrefix !== undefined) {
				console.log(`${context.middlewareOptions.loggingPrefix}: ${url}`);
			} else {
				console.log(url);
			}
			await this.nextMiddleware.execute(context);
		} catch (error) {
			throw error;
		}
	}

	public setNext(next: Middleware): void {
		this.nextMiddleware = next;
	}
}
```

## Request builder options

### Version

Passing in a version through `.version()` has the highest priority. It overrides the Microsoft Graph client default version from `.init()` and the global library default (currently v1.0).

```typescript
try {
	let res = await client
		.api("/me/ownedDevices")
		.version("beta")
		.get();
	console.log(res);
} catch (error) {
	throw error;
}
```

### Query

You can pass in any URL query parameters through `.query()` as a dictionary or string.

```typescript
try {
	// Below three statements are same
	let res1 = await client
		.api("/me")
		.query({ $select: "displayName" })
		.get();
	let res2 = await client
		.api("/me")
		.query("$select=displayName")
		.get();
	let res3 = await client
		.api("/me")
		.select("displayName")
		.get();
} catch (error) {
	throw error;
}
```

### Headers

You can pass in additional request headers through `.header()` or `.headers()` either individually or in a dictionary.

```typescript
try {
	let messageBody = {
		message: {
			subject: "Meet for lunch?",
			body: {
				contentType: "Text",
				content: "The new cafeteria is open.",
			},
			toRecipients: [
				{
					emailAddress: {
						address: "garthf@contoso.com",
					},
				},
			],
		},
	};
	// Below two statements are same
	let res1 = await client
		.api("/me/sendMail")
		.header("content-type", "application/json")
		.post(messageBody);
	let res2 = await client
		.api("/me/sendMail")
		.headers({ "content-type": "application/json" })
		.post(messageBody);
} catch (error) {
	throw error;
}
```

### Options

You can pass in additional request options through `.option()` and `.options()`, either individually or in a dictionary. Options can be [node specific](https://github.com/bitinn/node-fetch#options) or [from fetch standard](https://fetch.spec.whatwg.org/#requestinit)

```typescript
let HttpProxyAgent = require('http-proxy-agent');
try {
    // HTTP/HTTPS proxy to connect to
    let proxy = <YOUR PROXY>;
    let agent = new HttpProxyAgent(proxy);
    // Below two statements are same
    let res1 = await client.api("/me").option("agent", agent).get();
    let res2 = await client.api("/me").options({agent: agent}).get();
} catch (error) {
    throw error;
}
```

## Query parameters

### \$select

`.select()` can take a string property, an array of strings or you can pass in each value as a separate argument.

```typescript
try {
	// Below three statements are same
	let res1 = await client
		.api("/me/people")
		.select("displayName")
		.select("department")
		.get();
	let res2 = await client
		.api("/me/people")
		.select("displayName", "department")
		.get();
	let res3 = await client
		.api("/me/people")
		.select(["displayName", "department"])
		.get();
} catch (error) {
	throw error;
}
```

### \$expand

`.expand()` can take a string property, an array of strings or you can pass in each value as a separate argument.

```typescript
try {
	// Below three statements are same
	let res1 = await client
		.api("/me/people")
		.expand("manager")
		.expand("directReports")
		.get();
	let res2 = await client
		.api("/me/people")
		.expand("manager", "directReports")
		.get();
	let res3 = await client
		.api("/me/people")
		.expand(["manager", "directReports"])
		.get();
} catch (error) {
	throw error;
}
```

### \$orderby

`.orderby()` can take a string property, an array of strings or you can pass in each value as a separate argument.

```typescript
try {
	// Below three statements are same
	let res1 = await client
		.api("/me/messages")
		.orderby("name")
		.orderby("subject")
		.get();
	let res2 = await client
		.api("/me/messages")
		.orderby("name", "subject")
		.get();
	let res3 = await client
		.api("/me/messages")
		.orderby(["name", "subject"])
		.get();
} catch (error) {
	throw error;
}
```

### \$top

`.top()` can take only a number as a parameter. Calling it multiple times is not supported.

```typescript
try {
	let res = await client
		.api("/me/contacts")
		.top(5)
		.get();
	console.log(res);
} catch (error) {
	throw error;
}
```

### \$skip

`.skip()` can take only a number as a parameter. Calling it multiple times is not supported.

```typescript
try {
	let res = await client
		.api("/me/events")
		.skip(10)
		.get();
	console.log(res);
} catch (error) {
	throw error;
}
```

### \$count

Set `.count()` to true to additionally return the number of objects in the collection.

```typescript
try {
	let res = await client
		.api("/me/calendars")
		.count(true)
		.get();
	console.log(res);
} catch (error) {
	throw error;
}
```

### \$filter

Pass a filter string to `.filter()` for filtering result collections. Calling filter multiple times will override previous filter strings.

```typescript
try {
	let res = await client
		.api("/users")
		.filter("startswith(displayName, 'dicaprio')")
		.get();
	console.log(res);
} catch (error) {
	throw error;
}
```

### \$search

Pass a search string to `.search()` to restrict the results of a request to match a search criterion. Calling search multiple times will override previous search strings.

```typescript
try {
	let res = await client
		.api("/me/people")
		.search("dicaprio")
		.get();
	console.log(res);
} catch (error) {
	throw error;
}
```

## Get the raw response

You can get the raw [Response Object](https://developer.mozilla.org/en-US/docs/Web/API/Response) using the client library by setting the raw response type.

```typescript
const rawResponse = await client
	.api("/me")
	.select("displayName")
	.responseType(ResponseType.RAW)
	.get();
console.log(rawResponse);
```

Using callback method,

```typescript
client.api("/me")
    .select("displayName")
    .responseType(ResponseType.RAW)
    .get((error, rawResponse) => {
		console.log(rawResponse);
	});
```
