---
title: 了解 Azure RBAC 中的角色定義 | Microsoft Docs
description: 了解角色型存取控制 (RBAC) 中的角色定義，以及如何定義自訂角色對 Azure 中的資源進行微調存取管理。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161811"
---
# <a name="understand-role-definitions"></a>了解角色定義

如果您想要嘗試了解角色的運作方式或建立自己的[自訂角色](custom-roles.md)，最好先了解如何定義角色。 本文詳細描述角色定義並提供一些範例。

## <a name="role-definition-structure"></a>角色定義結構

「角色定義」是權限集合。 有時簡稱為「角色」。 角色定義會列出可執行的作業，例如讀取、寫入和刪除。 它也會列出無法執行的作業。 角色定義的結構如下：

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

用來指定作業的字串具有下列格式：

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

作業字串的 `{action}` 部分指定您可以對資源類型執行的作業類型。 例如，您將會在 `{action}` 中看到下列子字串：

| 動作子字串    | 說明         |
| ------------------- | ------------------- |
| `*` | 此萬用字元會授與所有符合字串之作業的存取權。 |
| `read` | 啟用讀取作業 (GET)。 |
| `write` | 啟用寫入作業 (PUT、POST 和 PATCH)。 |
| `delete` | 啟用刪除作業 (DELETE)。 |

以下是 JSON 格式的[參與者](built-in-roles.md#contributor)角色定義。 `actions` 下的萬用字元 (`*`) 作業表示指派給這個角色的主體可以執行所有動作；換句話說，它可以管理所有項目。 這包括未來 Azure 新增資源類型時所定義的動作。 `notActions` 下的作業會從 `actions` 扣除。 如果是[參與者](built-in-roles.md#contributor)角色，`notActions` 會移除此角色管理資源存取權及指派資源存取權的功能。

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>管理作業

管理作業的角色型存取控制是在角色定義的 `actions` 和 `notActions` 區段中指定。 以下是 Azure 中的一些管理作業範例：

- 管理儲存體帳戶的存取權
- 建立、更新或刪除 Blob 容器
- 刪除資源群組及其所有資源

您的資料不會繼承管理存取權。 此隔離可防止具有萬用字元 (`*`) 的角色不受限地存取您的資料。 例如，如果使用者具有訂用帳戶的[讀取者](built-in-roles.md#reader)角色，則可以檢視儲存體帳戶，但無法檢視基礎資料。

## <a name="actions"></a>動作

`actions` 權限指定角色授與存取權的管理作業。 它是識別 Azure 資源提供者的安全性實體作業的作業字串集合。 以下是可用於 `actions` 中的一些管理作業範例。

| 作業字串    | 說明         |
| ------------------- | ------------------- |
| `*/read` | 授與所有 Azure 資源提供者的所有資源類型之讀取作業的存取權。|
| `Microsoft.Compute/*` | 授與對 Microsoft.Compute 資源提供者中所有資源類型之所有作業的存取權。|
| `Microsoft.Network/*/read` | 授與 Microsoft.Network 資源提供者的所有資源類型之讀取作業的存取權。|
| `Microsoft.Compute/virtualMachines/*` | 授與虛擬機器及其子資源類型之所有作業的存取權。|
| `microsoft.web/sites/restart/Action` | 授與重新啟動 Web 應用程式的存取權。|

## <a name="notactions"></a>notActions

`notActions` 權限指定從所允許 `actions` 中排除的管理作業。 如果排除限制的作業可更輕鬆地定義您要允許的作業集合，請使用 `notActions` 權限。 角色 (有效權限) 所授與之存取權的計算方式是將 `actions` 作業扣除 `notActions` 作業。

> [!NOTE]
> 如果為使用者指派會排除 `notActions` 中作業的角色，並指派授與相同作業存取權的第二個角色，即會允許使用者執行該作業。 `notActions` 不是拒絕規則 - 它只是一個便利的方式，可以在需要排除特定作業時建立允許的作業集合。
>

## <a name="assignablescopes"></a>assignableScopes

`assignableScopes` 區段指定角色可指派的範圍 (管理群組 (目前處於預覽階段)、訂用帳戶、資源群組或資源)。 您可以讓角色僅指派給需要它的訂用帳戶或資源群組，不會干擾其餘訂用帳戶或資源群組的使用者體驗。 您至少必須使用一個管理群組、訂用帳戶、資源群組或資源識別碼。

內建角色的 `assignableScopes` 設定為根目錄範圍 (`"/"`)。 根目錄範圍表示角色可指派給所有範圍。 您無法在自己的自訂角色中使用根目錄範圍。 如果您嘗試，則會收到授權錯誤。

有效的可指派範圍範例包括：

| 案例 | 範例 |
|----------|---------|
| 角色可指派給單一訂用帳戶 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| 角色可指派給兩個訂用帳戶 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| 角色只能指派給網路資源群組 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| 角色可指派給所有範圍 | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes 和自訂角色

自訂角色的 `assignableScopes` 區段也會控制誰可以建立、刪除、修改或檢視自訂角色。

| Task | 作業 | 說明 |
| --- | --- | --- |
| 建立/刪除自訂角色 | `Microsoft.Authorization/ roleDefinition/write` | 獲得授權可對自訂角色的所有 `assignableScopes` 執行此作業的使用者，可以建立 (或刪除) 用於這些範圍的自訂角色。 例如，訂用帳戶、資源群組和資源的[擁有者](built-in-roles.md#owner)和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)。 |
| 修改自訂角色 | `Microsoft.Authorization/ roleDefinition/write` | 獲得授權可對自訂角色的所有 `assignableScopes` 執行此作業的使用者，可以在這些範圍中修改自訂角色。 例如，訂用帳戶、資源群組和資源的[擁有者](built-in-roles.md#owner)和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)。 |
| 檢視自訂角色 | `Microsoft.Authorization/ roleDefinition/read` | 獲得授權可在範圍中執行此作業的使用者，可以檢視可指派給該範圍的自訂角色。 所有內建角色都允許指派自訂角色。 |

## <a name="role-definition-examples"></a>角色定義範例

下列範例使用 Azure CLI 示範[讀取者](built-in-roles.md#reader)角色定義，如下所示：

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

下列範例使用 Azure PowerShell 示範可監視和重新啟動虛擬機器的自訂角色，如下所示：

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>另請參閱

* [內建角色](built-in-roles.md)
* [自訂角色](custom-roles.md)
* [Azure Resource Manager 資源提供者作業](resource-provider-operations.md)
