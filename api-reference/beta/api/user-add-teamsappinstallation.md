---
title: "Install app for user"
description: "Install an app in the personal scope of the specified user."
author: "clearab"
doc_type: "apiPageType"
localization_priority: Normal
ms.prod: "microsoft-teams"
---

# Install app for user

[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]

Install an [app](../resources/teamsapp.md) in the personal scope of the specified [user](../resources/user.md).

## Permissions

One of the following permissions is required to call this API. To learn more, including how to choose permissions, see [Permissions](/graph/permissions-reference).

|Permission type      | Permissions (from least to most privileged)              |
|:--------------------|:---------------------------------------------------------|
|Delegated (work or school account) |  User.ReadWrite.All, Directory.ReadWrite.All     |
|Delegated (personal Microsoft account) | Not supported.    |
|Application | User.ReadWrite.All, Directory.ReadWrite.All |

## HTTP request
<!-- { "blockType": "ignored" } -->
```http
POST /users/{id}/teamwork/installedApps
```

## Request headers

| Header       | Value |
|:---------------|:--------|
| Authorization  | Bearer {token}. Required.  |

## Request body

The request body should contain the ID of the existing catalog app to be added.

| Property   | Type |Description|
|:---------------|:--------|:----------|
|teamsApp|String|The ID of the app to add.|

## Response

If successful, this method returns a `201 Created` response code. It does not return anything in the response body.

## Example

### Request

The following is an example of the request.
<!-- {
  "blockType": "request",
  "name": "user_add_teamsApp"
}-->
```http
POST https://graph.microsoft.com/beta/users/{id}/teamwork/installedApps
Content-type: application/json

{
   "teamsApp@odata.bind":"https://graph.microsoft.com/beta/appCatalogs/teamsApps/12345678-9abc-def0-123456789a"
}
```

### Response

The following is an example of the response.

<!-- {
  "blockType": "response",
  "truncated": true
} -->

```http
HTTP/1.1 201 Created
```

<!-- uuid: 8fcb5dbc-d5aa-4681-8e31-b001d5168d79
2015-10-25 14:57:30 UTC -->
<!-- {
  "type": "#page.annotation",
  "description": "User add teamsAppInstallations",
  "keywords": "",
  "section": "documentation",
  "tocPath": ""
}-->
