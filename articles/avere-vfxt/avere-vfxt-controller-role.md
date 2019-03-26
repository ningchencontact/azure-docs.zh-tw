---
title: 自訂的控制器存取角色 - Avere vFXT for Azure
description: 如何為 Avere vFXT 叢集控制器建立自訂存取角色
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 2a0f4a628764aaa561a5567d3435a42da804a994
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417832"
---
# <a name="customized-controller-access-role"></a>自訂的控制器存取角色

Avere vFXT for Azure 叢集控制器會利用受控識別和角色型存取控制 (RBAC) 來建立和管理叢集。 

依預設會將[內建的擁有者角色](../role-based-access-control/built-in-roles.md#owner)指派給叢集控制器。 此外，控制器的存取範圍會限定為其資源群組 - 它無法修改叢集資源群組以外的元素。

本文說明您如何自行為叢集控制器建立存取角色，而不是使用預設設定。 

## <a name="edit-the-role-prototype"></a>編輯角色原型

請從 <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt> 所提供的原型角色開始著手。

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/read",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

在 AssignableScopes 陳述式中新增 Avere vFXT for Azure 的訂用帳戶識別碼。 自訂名稱，並視需要新增或改變定義。 

若要限制權限，請謹慎為之。 如果控制器沒有足夠的存取權，叢集建立作業可能會失敗。 

若想了解叢集控制器建立叢集所需的權限，請[開立支援票證](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)。 

將您的自訂角色定義儲存為 .json 檔案。 

## <a name="define-the-role"></a>定義角色 

請遵循下列步驟，將自訂角色定義新增至您的訂用帳戶。 

1. 在 Azure 入口網站中開啟 Azure Cloud Shell 或瀏覽到 [https://shell.azure.com](https://shell.azure.com)。

1. 使用 Azure CLI 命令來切換到您的 vFXT 訂用帳戶：

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. 建立角色：

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   請使用您的檔案名稱和路徑取代此範例中的 ```/avere-contributor-custom.json```。 

儲存角色定義命令的輸出 - 其中包含您需要提供給叢集建立範本的角色識別碼。 

## <a name="find-the-role-id"></a>尋找角色識別碼

Avere vFXT 部署範本必須要有角色的全域唯一識別碼 (GUID)，才能為控制器指派自訂角色。 

角色 GUID 是包含 32 個字元的字串，格式如下：8e3af657-a8ff-443c-a75c-2fe8c4bcb635

若要查閱角色的 GUID，請使用下列命令，並在 ```--name``` 參數中使用您的角色名稱。

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
部署 Avere vFXT for Azure 時，請在 [Avere 叢集建立角色識別碼] 欄位中輸入此字串。

## <a name="next-steps"></a>後續步驟

閱讀[部署 vFXT 叢集](avere-vfxt-deploy.md)，了解如何部署 Avere vFXT for Azure
