---
title: "searchRequest resource type"
description: "PROVIDE DESCRIPTION HERE"
localization_priority: Normal
author: "nmoreau"
ms.prod: "Search"
doc_type: "resourcePageType"
---

# searchRequest resource type

PROVIDE DESCRIPTION HERE

## Properties

| Property     | Type        | Description |
|:-------------|:------------|:------------|
|_sources|String collection||
|enableTopResults|Boolean||
|entityType|String| Possible values are: `event`, `message`, `driveItem`.|
|from|Int32||
|query|[searchQuery](searchquery.md)||
|size|Int32||

## JSON representation

The following is a JSON representation of the resource.

<!-- {
  "blockType": "resource",
  "optionalProperties": [

  ],
  "@odata.type": "microsoft.graph.searchRequest",
  "baseType": null
}-->

```json
{
  "_sources": ["String"],
  "enableTopResults": true,
  "entityType": "String",
  "from": 1024,
  "query": {"@odata.type": "microsoft.graph.searchQuery"},
  "size": 1024
}
```

<!-- uuid: 16cd6b66-4b1a-43a1-adaf-3a886856ed98
2019-02-04 14:57:30 UTC -->
<!-- {
  "type": "#page.annotation",
  "description": "searchRequest resource",
  "keywords": "",
  "section": "documentation",
  "tocPath": ""
}-->