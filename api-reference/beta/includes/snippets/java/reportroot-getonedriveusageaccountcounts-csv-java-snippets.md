---
description: "Automatically generated file. DO NOT MODIFY"
---

```java

IGraphServiceClient graphClient = GraphServiceClient.builder().authenticationProvider( authProvider ).buildClient();

IOneDriveUsageAccountCountsCollectionPage getOneDriveUsageAccountCounts = graphClient.reports()
	.getOneDriveUsageAccountCounts('D7')
	.buildRequest()
	.get();

```