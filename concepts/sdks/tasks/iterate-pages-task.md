---
title: "Iterate across paged results"
description: "Describes how you can control paging across a paged result set."
localization_priority: Normal
author: MichaelMainer
---

# Page iteration

Microsoft Graph queries can result in paged result sets. The Microsoft Graph SDKs provide page iterator objects to help you control paging across the result sets. You can learn more about Microsoft Graph paging by reading the [Paging Microsoft Graph data in your app](../../paging.md).

## Iterate over all pages in a result set

# [C#](#tab/CS)

<!-- TODO -->

```csharp

```

# [Javascript](#tab/Javascript)

```typescript
async function callingPattern() {
	try {
		// Makes request to fetch mails list. Which is expected to have multiple pages of data.
		let response: PageCollection = await client.api("/me/messages").get();
		// A callback function to be called for every item in the collection. This call back should return boolean indicating whether not to continue the iteration process.
		let callback: PageIteratorCallback = (data) => {
			console.log(data);
			return true;
		};
		// Creating a new page iterator instance with client a graph client instance, page collection response from request and callback
		let pageIterator = new PageIterator(client, response, callback);
		// This iterates the collection until the nextLink is drained out.
		pageIterator.iterate();
	} catch (e) {
		throw e;
	}
}
```

---

## Stop and resume iteration

# [C#](#tab/CS)

<!-- TODO -->

```csharp

```

# [Javascript](#tab/Javascript)

```typescript
// Populating custom size pages if the api restricts to some maximum size. Lazy loading more data on user prompt or something, stop and resume will do the trick.
async function customSize() {
	try {
		let response: PageCollection = await client.api("/me/messages").get();
		let size = 1000;
		let count = 0;
		let callback: PageIteratorCallback = (data) => {
			console.log(data);
			count++;
			if (count === size) {
				count = 0;
				return false;
			}
			return true;
		};
		let pageIterator = new PageIterator(client, response, callback);
		// This stops iterating over for 1000 entities.
		pageIterator.iterate();

		// Resuming will do start from where it left off and iterate for next 1000 entities.
		// Check and resume is likely to be called in any user interaction requiring to load more data.
		if (!pageIterator.isComplete()) {
			pageIterator.resume();
		}
	} catch (e) {
		throw e;
	}
}
```

---