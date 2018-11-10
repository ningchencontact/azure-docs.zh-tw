---
title: Avere vFXT 必要條件 - Azure
description: Avere vFXT for Azure 的必要條件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 823bf50a54ff43fa95f7136c137e3d8f3303c3e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669525"
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
* 允許叢集控制器節點管理資源群組和虛擬網路 
* 允許叢集控制器建立及修改叢集節點 

如果您不想將擁有者存取權提供給建立 vFXT 的使用者，可用兩種方式來因應：

* 在符合下列條件時，資源群組擁有者可建立叢集：

  * 訂用帳戶擁有者必須[接受 Avere vFXT 軟體條款](#accept-software-terms-in-advance)，並[建立叢集節點存取角色](avere-vfxt-deploy.md#create-the-cluster-node-access-role)。
  * 所有 Avere vFXT 資源都必須部署在資源群組內，包括：
    * 叢集控制器
    * 叢集節點
    * Blob 儲存體
    * 網路元素
 
* 如果事先建立了額外的存取角色，並指派給使用者，則沒有擁有者權限的使用者可以建立 vFXT 叢集。 不過，此角色會讓使用者獲得重要的權限。 [本文](avere-vfxt-non-owner.md)說明為非擁有者授與建立叢集的權限。

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 叢集配額

您必須有下列 Azure 元件的足夠配額。 如有需要，請[要求增加配額](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此處所列的是 vFXT 叢集本身的虛擬機器和 SSD 元件。 針對預計要用於計算陣列的 VM 和 SSD，您還需要額外的配額。  請確定您在要執行工作流程的區域中可獲得配額。

|Azure 元件|Quota|
|----------|-----------|
|虛擬機器|3 個或更多 D16s_v3 或 E32s_v3|
|進階 SSD 儲存體|200 GB 的 OS 空間，外加每個節點 1 TB 到 4 TB 的快取空間 |
|儲存體帳戶 (選擇性) |v2|
|資料後端儲存體 (選擇性) |一個新的 LRS Blob 容器 |

## <a name="accept-software-terms-in-advance"></a>事先接受軟體條款

> [!NOTE] 
> 如果訂用帳戶擁有者建立了 Avere vFXT 叢集，則不需要此步驟。

您必須接受 Avere vFXT 軟體的服務條款，才能建立叢集。 如果您不是訂用帳戶擁有者，請由訂用帳戶擁有者事先接受這項條款。 每個訂用帳戶只需要執行此步驟一次。

若要事先接受軟體條款： 

1. 在 Azure 入口網站中或瀏覽到 <https://shell.azure.com>，以開啟 Cloud Shell。 使用您的訂用帳戶識別碼登入。

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 發出下列命令以接受服務條款，並啟用 Avere vFXT for Azure 軟體映像的程式設計存取： 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-node:latest
   ```

## <a name="next-step-create-the-vfxt-cluster"></a>下一個步驟：建立 vFXT 叢集

完成這些必要條件後，您即可建立叢集本身。 如需指示，請參閱[部署 vFXT 叢集](avere-vfxt-deploy.md)。