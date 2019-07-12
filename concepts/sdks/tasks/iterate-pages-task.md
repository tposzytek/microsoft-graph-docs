---
title: "Iterate across paged results"
description: "Describes how you can control paging across a paged result set."
localization_priority: Normal
author: sdk-contributors
---

# Page iteration

Microsoft Graph queries can result in paged result sets. The Microsoft Graph SDKs provide page iterator objects to help you control paging across the result sets. You can learn more about Microsoft Graph paging by reading the [Paging Microsoft Graph data in your app](../../paging.md).

## Iterate over all pages in a result set

# [C#](#tab/CS)

```csharp
// Get an initial page results to populate the iterator.
IUserEventsCollectionPage iUserEventsCollectionPage = await graphClient.Me
                                                                       .Events
                                                                       .Request()
                                                                       .Top(2)
                                                                       .GetAsync();

// Create the callback to process each entity returned in the pages
Func<Event,bool> processEachEvent = (e) =>
{
    Console.Writeline(e.Subject);
    return true;
};

// Create the iterator with the specified type
var eventPageIterator = PageIterator<Event>.CreatePageIterator(graphClient,
                                                               iUserEventsCollectionPage,
                                                               processEachEvent);

await eventPageIterator.IterateAsync();

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

This example shows how to iterate over a delta query and resume iterating after
the initial sync has completed.

```csharp
// Get an initial page results to populate the iterator.
var messagesDeltaCollectionPage = await graphClient.Me
                                                    .MailFolders["inbox"]
                                                    .Messages
                                                    .Delta()
                                                    .Request()
                                                    .GetAsync();

// Create the function to process each entity returned in the pages
Func<Message, bool> processEachMessage = (e) =>
{
    Debug.WriteLine($"Message subject: {e.Subject}");
    return true;
};

// This requires the dev to specify the generic type in the CollectionPage.
var messagePageIterator = PageIterator<Message>.CreatePageIterator(graphClient,
                                                messagesDeltaCollectionPage,
                                                processEachMessage);

await messagePageIterator.IterateAsync();

var me = await graphClient.Me.Request().GetAsync();
var recipients = new List<Recipient>()
{
    new Recipient()
    {
        EmailAddress = new EmailAddress()
        {
            Address = me.Mail
        }
    }
};

var message = new Message()
{
    Subject = "Message sent after deltatoken received.",
    ToRecipients = recipients
};
await graphClient.Me.SendMail(message, true).Request().PostAsync();

await Task.Delay(3000);

await messagePageIterator.ResumeAsync();
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