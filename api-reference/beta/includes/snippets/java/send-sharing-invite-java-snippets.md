---
description: "Automatically generated file. DO NOT MODIFY"
---

```java

IGraphServiceClient graphClient = GraphServiceClient.builder().authenticationProvider( authProvider ).buildClient();

LinkedList<DriveRecipient> recipientsList = new LinkedList<DriveRecipient>();
DriveRecipient recipients = new DriveRecipient();
recipients.email = "ryan@contoso.org";

recipientsList.add(recipients);

String message = "Here's the file that we're collaborating on.";

boolean requireSignIn = True;

boolean sendInvitation = True;

LinkedList<String> rolesList = new LinkedList<String>();
rolesList.add("write");

String password = "password123";

int expirationDateTime = 7/15/2018 2:00:00 PM;

graphClient.me().drive().items("{item-id}")
	.invite(requireSignIn,rolesList,sendInvitation,message,recipientsList,expirationDateTime,password)
	.buildRequest()
	.post();

```