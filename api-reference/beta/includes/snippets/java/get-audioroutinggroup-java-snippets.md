---
description: "Automatically generated file. DO NOT MODIFY"
---

```java

IGraphServiceClient graphClient = GraphServiceClient.builder().authenticationProvider( authProvider ).buildClient();

AudioRoutingGroup AudioRoutingGroup = graphClient.app().calls("{id}").audioRoutingGroups("{id}")
	.buildRequest()
	.get();

```