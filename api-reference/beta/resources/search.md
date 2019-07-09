---
title: "search resource type"
description: "PROVIDE DESCRIPTION HERE"
localization_priority: Normal
author: "nmoreau"
ms.prod: "Search"
doc_type: "resourcePageType"
---

# search resource type

PROVIDE DESCRIPTION HERE

## Methods

| Method       | Return Type | Description |
|:-------------|:------------|:------------|
| [Get search](../api/search-get.md) | [search](search.md) | Read properties and relationships of search object. |
| [Update](../api/search-update.md) | [search](search.md) | Update search object. |
| [Delete](../api/search-delete.md) | None | Delete search object. |

## Properties

| Property     | Type        | Description |
|:-------------|:------------|:------------|
|requests|[searchRequest](searchrequest.md) collection||
|value|[searchResponse](searchresult.md) collection||

## Relationships

None

## JSON representation

The following is a JSON representation of the resource.

<!-- {
  "blockType": "resource",
  "optionalProperties": [

  ],
  "@odata.type": "microsoft.graph.search",
  "baseType": ""
}-->

```json
{
  "requests": [{"@odata.type": "microsoft.graph.searchRequest"}],
  "value": [{"@odata.type": "microsoft.graph.searchResponse"}]
}
```

<!-- uuid: 16cd6b66-4b1a-43a1-adaf-3a886856ed98
2019-02-04 14:57:30 UTC -->
<!-- {
  "type": "#page.annotation",
  "description": "search resource",
  "keywords": "",
  "section": "documentation",
  "tocPath": ""
}-->