---
title: 在沒有控制器的情況下建立 Avere 角色 - Avere vFXT for Azure
description: 在沒有叢集控制器 VM 的情況下建立必要 RBAC 角色的方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670025"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>在沒有控制器的情況下建立 Avere vFXT 叢集執行階段存取角色

此文件說明在建立叢集控制器 VM 之前從命令列建立叢集節點存取角色的方法。 

若要從叢集控制器建立它，請閱讀[建立叢集節點存取角色](avere-vfxt-deploy.md#create-the-cluster-node-access-role)。 控制器映像包括角色原型檔案。 您可以使用您的訂用帳戶識別碼更新該檔案，並使用它來定義位於控制器 VM 本機的角色。

## <a name="create-an-azure-rbac-role"></a>建立 Azure RBAC 角色

Avere vFXT 系統使用[角色型存取控制](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) 來授權 vFXT 叢集節點執行必要工作。  

在執行標準 vFXT 叢集作業時，個別 vFXT 節點必須執行諸如讀取 Azure 資源屬性、管理儲存體及控制其他節點之網路介面設定等作業。 

此角色僅供 vFXT 節點使用，而非供叢集控制器 VM 使用。  

若要在建立控制器之前建立角色，請依照下列步驟執行： 

1. 在 Azure 入口網站中開啟 Azure Cloud Shell 或瀏覽到 [https://shell.azure.com](https://shell.azure.com)。

1. 使用 Azure CLI 命令來切換到您的 vFXT 訂用帳戶：

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. 使用這些命令從 Marketplace 映像下載角色並編輯它。 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. 編輯檔案以包括您的訂用帳戶識別碼並刪除其上的一行。 將檔案儲存為 ``avere-cluster.json``。

   ![顯示訂用帳戶識別碼與選取要刪除之「移除此行」的主控台文字編輯器](media/avere-vfxt-edit-role.png)

5. 建立角色：  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

當您建立叢集時，請提供角色名稱 (在此案例中是 `avere-cluster`)。 叢集建立指令碼會將該角色指派到新建立的叢集節點。 

## <a name="sample-cluster-node-runtime-role-definition"></a>範例叢集節點執行階段角色定義

> [!IMPORTANT] 
> 此範例定義取自產品的公開推出 (GA) 前版本。 若您隨著目前產品發行版本取得的版本不同，請改為使用該版本。

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
    "NotActions": [],
    "Actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
    ],
    "DataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
    ]
}

```