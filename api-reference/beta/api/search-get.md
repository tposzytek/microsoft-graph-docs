---
title: "Get search" MOREAUTODO : Document this.
description: "Retrieve the properties and relationships of search object."
localization_priority: Normal
author: "nmoreau"
ms.prod: "Search"
doc_type: "apiPageType"
---

# Get search

Retrieve the properties and relationships of search object.

## Permissions

One of the following permissions is required to call this API. To learn more, including how to choose permissions, see [Permissions](/graph/permissions-reference).

| Permission type                        | Permissions (from least to most privileged) |
|:---------------------------------------|:--------------------------------------------|
| Delegated (work or school account)     | Not supported. |
| Delegated (personal Microsoft account) | Not supported. |
| Application                            | Not supported. |

## HTTP request

<!-- { "blockType": "ignored" } -->

```http
GET /search
```

## Optional query parameters

This method supports some of the OData query parameters to help customize the response. For general information, see [OData Query Parameters](/graph/query-parameters)

## Request headers

| Name      |Description|
|:----------|:----------|
| Authorization | Bearer {code} |

## Request body

Do not supply a request body for this method.

## Response

If successful, this method returns a `200 OK` response code and the requested [search](../resources/search.md) object in the response body.

## Examples

### Request

The following is an example of the request.
<!-- {
  "blockType": "request",
  "name": "get_search"
}-->

```http
GET https://graph.microsoft.com/v1.0/search
```

### Response

The following is an example of the response.

> [!NOTE]
> The response object shown here might be shortened for readability. All the properties will be returned from an actual call.

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.search"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
    {
      "searchTerms": [
        "searchTerms-value"
      ],
      "hitsContainers": [
        {
          "hits": [
            {
              "_id": "_id-value",
              "_score": 99,
              "_sortField": "_sortField-value",
              "_summary": "_summary-value"
            }
          ],
          "total": 99,
          "moreResultsAvailable": true
        }
      ]
    }
  ],
  "requests": [
    {
      "entityType": "entityType-value",
      "query": {
        "query_string": {
          "query": "query-value"
        },
        "filter": {
          "bool": [

          ],
          "should": [

          ],
          "term": {
          },
          "range": {
          }
        }
      },
      "from": 99,
      "size": 99,
      "_sources": [
        "_sources-value"
      ],
      "enableTopResults": true
    }
  ]
}
```

<!-- uuid: 16cd6b66-4b1a-43a1-adaf-3a886856ed98
2019-02-04 14:57:30 UTC -->
<!-- {
  "type": "#page.annotation",
  "description": "Get search",
  "keywords": "",
  "section": "documentation",
  "tocPath": ""
}-->