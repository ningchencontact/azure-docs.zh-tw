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
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 9bb7808f2b483fe9cd7d22c6df3fe80d4a98f1f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266851"
---
# <a name="understand-role-definitions"></a>了解角色定義

如果您想要嘗試了解角色的運作方式或建立自己的[自訂角色](custom-roles.md)，最好先了解如何定義角色。 本文詳細描述角色定義並提供一些範例。

## <a name="role-definition-structure"></a>角色定義結構

「角色定義」是權限集合。 有時簡稱為「角色」。 角色定義會列出可執行的作業，例如讀取、寫入和刪除。 也可能列出無法執行的作業或與基礎資料相關的作業。 角色定義的結構如下：

```
assignableScopes []
description
id
name
permissions []
  actions []
  dataActions []
  notActions []
  notDataActions []
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
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-and-data-operations-preview"></a>管理和資料作業 (預覽)

管理作業的角色型存取控制是在角色定義的 `actions` 和 `notActions` 區段中指定。 以下是 Azure 中的一些管理作業範例：

- 管理儲存體帳戶的存取權
- 建立、更新或刪除 Blob 容器
- 刪除資源群組及其所有資源

您的資料不會繼承管理存取權。 此隔離可防止具有萬用字元 (`*`) 的角色不受限地存取您的資料。 例如，如果使用者具有訂用帳戶的[讀取者](built-in-roles.md#reader)角色，則可以檢視儲存體帳戶，但預設為無法檢視基礎資料。

在此之前，資料作業不可使用角色型存取控制。 資料作業的授權會因為資源提供者不同而有差異。 但管理作業所使用的相同角色型存取控制授權模型已延伸到資料作業 (目前處於預覽狀態)。

為支援資料作業，已新增資料區段到角色定義結構中。 資料作業會在 `dataActions` 和 `notDataActions` 區段中指定。 藉由新增這些資料區段，可繼續維持管理和資料之間的分隔。 這可避免目前具有萬用字元 (`*`) 的角色指派突然存取資料。 以下是可在 `dataActions` 和 `notDataActions`中指定的一些資料作業：

- 讀取容器中的 Blob 清單
- 將儲存體 Blob 寫入容器中
- 刪除佇列中的訊息

以下是[儲存體 Blob 資料讀取器 (預覽)](built-in-roles.md#storage-blob-data-reader-preview) 的角色定義，其中包含 `actions` 和 `dataActions` 區段中的作業。 此角色可讓您讀取 Blob 容器和基礎 Blob 資料。

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Allows for read access to Azure Storage blob containers and data.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/read"
        ],
        "additionalProperties": {},
        "dataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
        ],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Storage Blob Data Reader (Preview)",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

您只能將資料作業新增至 `dataActions` 和 `notDataActions` 區段。 資源提供者會藉由將 `isDataAction` 屬性設為 `true`，來識別哪些作業是資料作業。 若要查看 `isDataAction` 為 `true` 的作業清單，請參閱[資源提供者作業](resource-provider-operations.md)。 在角色定義中，沒有資料作業的角色不需要有 `dataActions` 和 `notDataActions` 區段。

所有管理作業 API 呼叫的授權都會由 Azure Resource Manager 處理。 資料作業 API 呼叫的授權是由資源提供者或 Azure Resource Manager 處理。

### <a name="data-operations-example"></a>資料作業範例

若要進一步了解管理和資料作業如何運作，我們來看特定範例。 Alice 已在訂用帳戶範圍上獲得[擁有者](built-in-roles.md#owner)角色的指派。 Bob 已在儲存體帳戶範圍上獲得[儲存體 Blob 資料參與者 (預覽)](built-in-roles.md#storage-blob-data-contributor-preview) 角色的指派。 此範例如下圖所示。

![角色型存取控制已延伸為可支援管理和資料作業](./media/role-definitions/rbac-management-data.png)

Alice 的[擁有者](built-in-roles.md#owner)角色和 Bob 的[儲存體 Blob 資料參與者 (預覽)](built-in-roles.md#storage-blob-data-contributor-preview) 角色有下列動作：

擁有者

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

儲存體 Blob 資料參與者 (預覽)

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

由於 Alice 在訂用帳戶範圍上有萬用字元 (`*`) 動作，她的權限可向下繼承，因此她可執行所有管理動作。 不過，Alice 無法執行資料作業。 例如，根據預設，Alice 無法讀取容器內的 Blob，但她可以讀取、寫入和刪除容器。

Bob 的權限僅限於[儲存體 Blob 資料參與者 (預覽)](built-in-roles.md#storage-blob-data-contributor-preview) 角色中指定的 `actions` 和 `dataActions`。 以此角色為基礎，Bob 可以執行管理和資料作業。 例如，Bob 可以讀取、寫入和刪除指定儲存體帳戶中的容器，而且他也可以讀取、寫入和刪除 Blob。

### <a name="what-tools-support-using-rbac-for-data-operations"></a>哪些工具支援對資料作業使用 RBAC？

若要檢視及使用資料作業，您必須有正確版本的工具或 SDK：

| 工具  | 版本  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-azurerm-ps) | 5.6.0 或更新版本 |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 或更新版本 |
| [Azure for .NET](/dotnet/azure/) | 2.8.0-預覽或更新版本 |
| [Azure SDK for Go](/go/azure/azure-sdk-go-install) | 15.0.0 或更新版本 |
| [Azure for Java](/java/azure/) | 1.9.0 或更新版本 |
| [Azure for Python](/python/azure) | 0.40.0 或更新版本 |
| [Azure SDK for Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 或更新版本 |

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

## <a name="dataactions-preview"></a>dataActions (預覽)

`dataActions` 權限可對該物件中的資料指定資料作業 (資料作業的存取權是角色所授與)。 例如，如果使用者有儲存體帳戶的讀取 Blob 資料存取權，則他們可讀取該儲存體帳戶中的 Blob。 以下是可用於 `dataActions` 中的一些資料作業範例。

| 作業字串    | 說明         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | 傳回 Blob 或 Blob 清單。 |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | 傳回寫入 Blob 的結果。 |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | 傳回訊息。 |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | 傳回訊息或寫入或刪除訊息的結果。 |

## <a name="notdataactions-preview"></a>notDataActions (預覽)

`notDataActions` 權限可指定從所允許 `dataActions` 中排除的資料作業。 角色 (有效權限) 所授與之存取權的計算方式是將 `dataActions` 作業扣除 `notDataActions` 作業。 每個資源提供者會提供個別的一組 API 來完成資料作業。

> [!NOTE]
> 如果為使用者指派的角色已在 `notDataActions` 中排除一個資料作業，並且指派授與相同資料作業存取權的第二個角色給使用者，即會允許使用者執行該資料作業。 `notDataActions` 不是拒絕規則 - 它只是一個便利的方式，可以在需要排除特定資料作業時建立允許的資料作業集合。
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
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
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
  "DataActions":  [

                  ],
  "NotDataActions":  [

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
