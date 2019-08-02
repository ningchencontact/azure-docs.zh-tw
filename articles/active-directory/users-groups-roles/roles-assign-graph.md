---
title: 使用 Microsoft Graph API Azure Active Directory 來指派和移除自訂系統管理員角色 |Microsoft Docs
description: 在 Azure Active Directory 中使用圖形 API 指派和移除 Azure AD 系統管理員角色
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 898f444e868a469aed5358f49f48f5bcbfab4450
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707574"
---
# <a name="assign-custom-admin-roles-using-graph-api-in-azure-active-directory"></a>在 Azure Active Directory 中使用圖形 API 指派自訂系統管理員角色 

您可以自動化將角色指派給使用者帳戶 Microsoft Graph API 的方式。 本文涵蓋 roleAssignments 上的 POST、GET 及 DELETE 作業。

## <a name="required-permissions"></a>必要權限

使用全域管理員帳戶或特殊許可權身分識別管理員連接到您的 Azure AD 租使用者, 以指派或移除角色。

## <a name="post-operations-on-roleassignment"></a>RoleAssignment 上的 POST 作業

建立使用者與角色定義之間角色指派的 HTTP 要求。

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

本文

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

回應

``` HTTP
HTTP/1.1 201 Created
```

建立主體或角色定義不存在之角色指派的 HTTP 要求

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

本文

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/"]
}
```

回應

``` HTTP
HTTP/1.1 404 Not Found
```

在內建角色定義上建立單一資源範圍角色指派的 HTTP 要求。

> [!NOTE] 
> 現今的內建角色有限制, 其範圍僅限於「/」整個組織範圍或「/AU/*」範圍。 單一資源範圍不適用於內建角色, 但適用于自訂角色。

POST

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal
```

本文

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":["/ab2e1023-bddc-4038-9ac1-ad4843e7e539"]
}
```

回應

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>RoleAssignment 上的 GET 作業

取得指定主體之角色指派的 HTTP 要求

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=principalId eq ‘<object-id-of-principal>’
```

回應

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

針對指定的角色定義取得角色指派的 HTTP 要求。

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

回應

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

依識別碼取得角色指派的 HTTP 要求。

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

回應

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":["/"]
}
```

## <a name="delete-operations-on-roleassignment"></a>RoleAssignment 上的刪除作業

HTTP 要求, 用以刪除使用者與角色定義之間的角色指派。

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

回應
``` HTTP
HTTP/1.1 204 No Content
```

用來刪除不再存在之角色指派的 HTTP 要求

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

回應

``` HTTP
HTTP/1.1 404 Not Found
```

用來刪除自我和內建角色定義之間角色指派的 HTTP 要求

DELETE

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments/<id-of-role-assignment>?api-version=1.61-internal
```

回應

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

* 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
* 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
* 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
