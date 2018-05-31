---
title: 建立 Azure RBAC 的自訂角色 | Microsoft Docs
description: 了解如何使用 Azure 角色型存取控制來定義自訂角色，以在您的 Azure 訂用帳戶中進行更精確的身分識別管理。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e2ea46ea1a6b5bd3f50d4d4c15492c16c5241c0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161051"
---
# <a name="create-custom-roles-in-azure"></a>建立 Azure 的自訂角色

如果[內建角色](built-in-roles.md)不符合您特定的存取需求，您可以建立自己的自訂角色。 就像內建角色一樣，您可以將自訂角色指派給訂用帳戶、資源群組和資源範圍的使用者、群組和服務主體。 自訂角色會儲存在 Azure Active Directory (Azure AD) 租用戶中，而且可在訂用帳戶之間共用。 可以使用 Azure PowerShell、Azure CLI 和 REST API 建立自訂角色。 本文章會舉例說明，如何使用 PowerShell 及 Azure CLI 開始建立自訂角色。

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>建立自訂角色以使用 PowerShell 開啟支援要求

若要建立自訂角色，您可以從內建角色著手，加以編輯，然後建立新的角色。 在本範例中，會對內建的 [讀者][](built-in-roles.md#reader) 角色進行修改，建立了名為「讀者支援票證存取層級」的自訂角色。 它可讓使用者檢視訂用帳戶中的一切，也可開啟支援要求。

> [!NOTE]
> 可讓使用者開啟支援要求的唯獨兩個內建角色動作是[擁有者](built-in-roles.md#owner)和[參與者](built-in-roles.md#contributor)。 使用者若要開啟支援要求，必須獲指派訂用帳戶範圍內的角色，因為所有支援要求都會以 Azure 訂用帳戶作為基礎加以建立。

在 PowerShell 中，使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) 命令匯出 JSON 格式的[讀者](built-in-roles.md#reader)角色。

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

下圖顯示了 [讀者] [](built-in-roles.md#reader)角色的 JSON 輸出。 一般角色是由三個主要區段組成：`Actions`、`NotActions` 和 `AssignableScopes`。 `Actions` 區段會列出此角色所有允許的作業。 若要從 `Actions` 中排除作業，請將作業加入 `NotActions`。 有效使用權限的計算方式是將 `NotActions` 作業從 `Actions` 作業中扣除。

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

接下來，您可以編輯 JSON 輸出以建立自訂角色。 在此情況下，若要建立支援票證，必須新增 `Microsoft.Support/*` 作業。 每個作業都可由資源提供者提供。 若要取得資源提供者的作業清單，您可以使用 [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) 命令，或請參閱 [Azure Resource Manager 資源提供者作業][](resource-provider-operations.md)。

使用角色的位置必須包含明確的訂用帳戶識別碼。 訂用帳戶識別碼需列在 `AssignableScopes` 之下，否則不允許您將角色匯入您的訂用帳戶。

最後必須將 `IsCustom` 屬性設定為 `true`，來指定該角色為自訂角色。

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

若要建立新的自訂角色，可使用 [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 命令並提供更新的 JSON 角色定義檔案。

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

執行 [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 之後，新的自訂角色便可於 Azure 入口網站中使用，而且可以指派給使用者。

![匯入 Azure 入口網站之自訂角色的螢幕擷取畫面](./media/custom-roles/18.png)

![將自訂匯入的角色指派給相同目錄中之使用者的螢幕擷取畫面](./media/custom-roles/19.png)

![自訂匯入角色之權限的螢幕擷取畫面](./media/custom-roles/20.png)

具有此自訂角色的使用者可以建立新的支援要求。

![建立支援要求之自訂角色的螢幕擷取畫面](./media/custom-roles/21.png)

具有此自訂角色的使用者無法執行其他動作，例如建立 VM 或建立資源群組。

![無法建立 VM 之自訂角色的螢幕擷取畫面](./media/custom-roles/22.png)

![無法建立新 RG 之自訂角色的螢幕擷取畫面](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>建立自訂角色，才能使用 Azure CLI 開啟支援要求

使用 Azure CLI 與 PowerShell 建立自訂角色的步驟類似，除了 JSON 輸出有所不同以外。

在此範例中，您可以從內建[讀者](built-in-roles.md#reader)角色著手。 若要列出 [讀者][](built-in-roles.md#reader) 角色的動作，請使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list) 命令。

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

使用下列格式建立 JSON 檔案。 已在 `Actions` 區段中新增 `Microsoft.Support/*` 作業，讓該使用者可以開啟支援要求，同時繼續作為讀者。 您必須在 `AssignableScopes` 區段中新增要使用此角色的訂用帳戶識別碼。

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

若要建立新的自訂角色，請使用 [az role definition create](/cli/azure/role/definition#az_role_definition_create) 命令。

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

新的自訂角色現在可於 Azure 入口網站中使用，而要使用此角色的程序與先前 PowerShell 一節中程序的相同。

![使用 CLI 1.0 所建立之自訂角色的 Azure 入口網站螢幕擷取畫面](./media/custom-roles/26.png)


## <a name="see-also"></a>另請參閱
- [了解角色定義](role-definitions.md)
- [使用 Azure PowerShell 管理角色型存取控制](role-assignments-powershell.md)
- [使用 Azure CLI 管理角色型存取控制](role-assignments-cli.md)
- [使用 REST API 管理角色型存取控制](role-assignments-rest.md)
