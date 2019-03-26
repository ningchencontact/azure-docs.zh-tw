---
title: Avere vFXT 必要條件 - Azure
description: Avere vFXT for Azure 的必要條件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 04af92f21cecaa832e857a7017b67f815f6ab685
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417967"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>準備建立 Avere vFXT

本文說明建立 Avere vFXT 叢集的必要工作。

## <a name="create-a-new-subscription"></a>建立新的訂用帳戶

首先請建立新的 Azure 訂用帳戶。 讓每個 Avere vFXT 專案使用不同的訂用帳戶，可讓您輕鬆地追蹤所有專案資源與費用、在佈建期間防止其他專案導致可能的資源節流，以及簡化清除作業。  

若要在 Azure 入口網站中建立新的 Azure 訂用帳戶：

* 瀏覽至 [訂用帳戶](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 刀鋒視窗
* 按一下位於頂端的 [+ 新增] 按鈕
* 在出現提示時登入
* 選取供應項目，並逐步執行新建訂用帳戶的步驟

## <a name="configure-subscription-owner-permissions"></a>設定訂用帳戶擁有者權限

具有擁有者權限的使用者應建立 vFXT 叢集。 需要訂用帳戶擁有者權限的動作包括：

* 接受 Avere vFXT 軟體的條款
* 建立叢集節點存取角色 

如果您不想將擁有者存取權提供給建立 vFXT 的使用者，可用兩種方式來因應：

* 在符合下列條件時，資源群組擁有者可建立叢集：

  * 訂用帳戶擁有者必須[接受 Avere vFXT 軟體條款](#accept-software-terms)，並[建立叢集節點存取角色](#create-the-cluster-node-access-role)。 
  * 所有 Avere vFXT 資源都必須部署在資源群組內，包括：
    * 叢集控制器
    * 叢集節點
    * Blob 儲存體
    * 網路元素
 
* 對於沒有擁有者權限的使用者，可以事先使用角色型存取控制 (RBAC) 將權限指派給使用者，以建立 vFXT 叢集。 此方法會讓使用者獲得重要的權限。 [本文](avere-vfxt-non-owner.md)會說明如何建立存取角色，並為非擁有者授與建立叢集的權限。

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 叢集配額

您必須有下列 Azure 元件的足夠配額。 如有需要，請[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此處所列的是 vFXT 叢集本身的虛擬機器和 SSD 元件。 針對預計要用於計算陣列的 VM 和 SSD，您還需要額外的配額。  請確定您在要執行工作流程的區域中可獲得配額。

|Azure 元件|Quota|
|----------|-----------|
|虛擬機器|3 個或多個 E32s_v3|
|進階 SSD 儲存體|200 GB 的 OS 空間，外加每個節點 1 TB 到 4 TB 的快取空間 |
|儲存體帳戶 (選擇性) |v2|
|資料後端儲存體 (選擇性) |一個新的 LRS Blob 容器 |

## <a name="accept-software-terms"></a>接受軟體條款

> [!NOTE] 
> 如果訂用帳戶擁有者建立了 Avere vFXT 叢集，則不需要此步驟。

建立叢集的期間，您必須接受 Avere vFXT 軟體的服務條款。 如果您不是訂用帳戶擁有者，請由訂用帳戶擁有者事先接受這項條款。 每個訂用帳戶只需要執行此步驟一次。

若要事先接受軟體條款： 

1. 在 Azure 入口網站中或瀏覽到 <https://shell.azure.com>，以開啟 Cloud Shell。 使用您的訂用帳戶識別碼登入。

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 發出下列命令以接受服務條款，並啟用 Avere vFXT for Azure 軟體映像的程式設計存取： 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-access-roles"></a>建立存取角色 

[角色型存取控制](../role-based-access-control/index.yml) (RBAC) 會為 vFXT 叢集控制器和叢集節點提供授權來執行必要的工作。

* 叢集控制器需要有建立和修改 VM 的權限，才能建立叢集。 

* 作為標準叢集作業的一部分，個別 vFXT 節點必須執行諸如讀取 Azure 資源屬性、管理儲存體及控制其他節點網路介面設定等作業。

建立 Avere vFXT 叢集之前，您必須定義要搭配叢集節點使用的自訂角色。 

針對叢集控制器，您可以接受範本中的預設角色。 預設角色會將資源群組擁有者的權限提供給叢集控制器。 如果您想要為控制器建立自訂角色，請參閱[自訂控制器存取角色](avere-vfxt-controller-role.md)。

> [!NOTE] 
> 只有訂用帳戶擁有者，或是具有「擁有者」角色或「使用者存取系統管理員」角色的使用者可以建立角色。 您可以事先建立角色。  

### <a name="create-the-cluster-node-access-role"></a>建立叢集節點存取角色

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

您必須先建立叢集節點角色，才能建立 Avere vFXT for Azure 叢集。

> [!TIP] 
> Microsoft 內部使用者應使用名為「Avere 叢集執行階段操作員」的現有角色，而非嘗試建立一個角色。 

1. 複製此檔案。 在 AssignableScopes 列中加入您的訂用帳戶識別碼。

   (此檔案的目前版本會在 github.com/Azure/Avere 存放庫中儲存為 [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt)。)  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/read",
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

1. 將檔案儲存為 ``avere-operator.json``，或是類似的易記檔案名稱。 


1. 開啟 Azure Cloud shell，並使用您的訂用帳戶識別碼登入 (如[本文件](#accept-software-terms)稍早所述)。 使用此命令以建立角色：

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

建立叢集時會使用角色名稱。 在此範例中，該名稱為 ``avere-operator``。

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>在虛擬網路 （如有需要） 中建立儲存體服務端點

A[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)保留本機而不是路由虛擬網路外部的 Azure Blob 資料傳輸。 建議您針對任何 Avere vFXT 後, 端資料儲存體使用 Azure Blob 的 Azure 叢集。 

如果您是提供現有的 vnet，並為您的後端儲存體中建立新的 Azure Blob 容器，在叢集建立期間，您必須有 Microsoft 儲存體的 vnet 中的服務端點。 此端點必須存在，才能建立叢集，或建立將會失敗。 

即使稍後新增儲存體使用 Azure Blob 儲存體的 Azure 叢集的任何 Avere vFXT 並建議儲存體服務端點。 

> [!TIP] 
> * 如果您要在叢集建立期間建立新的虛擬網路，請略過此步驟。 
> * 這個步驟是選擇性，如果您未在叢集建立期間建立 Blob 儲存體。 在此情況下，您可以建立服務端點稍後如果您決定使用 Azure Blob。

從 Azure 入口網站中建立的儲存體服務端點。 

1. 從入口網站中，按一下左側的 [虛擬網路]。
1. 選取您的叢集的 vnet。 
1. 按一下左側的 [服務端點]。
1. 按一下頂端的 [新增]。
1. 將做為服務保持``Microsoft.Storage``選擇叢集的子網路。
1. 在底部按一下 [新增]。

   ![含有建立服務端點之註解的 Azure 入口網站螢幕擷取畫面](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>下一步：建立 vFXT 叢集

完成這些必要條件後，您即可建立叢集本身。 如需指示，請參閱[部署 vFXT 叢集](avere-vfxt-deploy.md)。