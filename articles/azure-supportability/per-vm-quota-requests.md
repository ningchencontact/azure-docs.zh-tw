---
title: Azure 每個 VM vCPU 配額增加要求 |Microsoft Docs
description: 每個 VM vCPU 配額增加要求數
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 280561126186e4c70399b3a1ddd177ff4eb54400
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850035"
---
# <a name="standard-quota-per-vm-series-vcpu-limit-increase"></a>標準配額：每個 VM 系列 vCPU 限制增加

Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額。 **隨用隨付 vm 和保留的 Vm 實例**會使用標準配額。 **找出 vm**會使用點配額。 隨用隨付和保留的 VM 實例的標準 vCPU 配額會針對每個區域中的每個訂用帳戶強制執行兩個層級

第一層是**總區域個 vcpu 限制**（跨所有 vm 系列），而第二層是**每個 vm 系列個 vcpu 限制**（例如 Dv3 系列個 vcpu）。 每當部署新的 VM 時，該 VM 系列新的和現有個 vcpu 使用量的總和，不得超過該特定 VM 系列所核准的 vCPU 配額。 此外，在所有 VM 系列上部署的新的和現有的 vCPU 計數，不應超過訂用帳戶所核准的區域個 vcpu 配額總計。 如果超過這些配額，將不允許 VM 部署。
您可以從 Azure 入口網站為 VM 系列要求增加個 vcpu 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域個 vcpu 限制。 

在 [[虛擬機器 vCPU 配額] 頁面](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和 [Azure 訂用帳戶[和服務限制] 頁面](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)上深入瞭解標準 vCPU 配額。 

[在這裡](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)深入瞭解如何增加標準配額的地區 vCPU 限制。 

[在這裡](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)深入瞭解**增加點 VM vCPU 限制**。

您可以透過入口網站中的 [說明 **+ 支援**] 分頁或 [**使用方式 + 配額**] 分頁，要求**每個 VM 系列的標準 vCPU 配額限制**增加。

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-the-help--support-blade"></a>使用 [說明 + 支援] 分頁，在訂用帳戶層級上每個 VM 系列的要求標準 vCPU 配額增加

請依照下列指示，透過 Azure 入口網站中提供的 Azure [說明 + 支援] 分頁來建立支援要求。 

您也可以透過單一支援案例來要求多個區域的配額。 如需詳細資訊，請參閱下面的步驟11。

1. 從 https://portal.azure.com 選取 [說明 **+ 支援**]。

   ![說明 + 支援](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  選取 [新增支援要求]。 

     ![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 [問題類型] 下拉式選單中，選擇 [**服務與訂用帳戶限制（配額）** ]。

   ![問題類型下拉式](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 選取需要增加配額的訂用帳戶。

   ![選取訂用帳戶 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 在 [**配額類型**] 下拉式選單中，選取 **[計算-VM （核心-個 vcpu）訂用帳戶限制增加**]。 

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. 在 [**問題詳細資料**] 中，按一下 [**提供詳細資料**]，提供其他資訊來協助處理您的要求。

   ![提供詳細資料](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 [**配額詳細資料**] 面板中，選取 [**部署模型**]，然後選取**位置。**

   ![配額詳細資料 DM](./media/resource-manager-core-quotas-request/1-7.png)

8. 針對選取的位置，選取 [**類型**值] 為 [**標準**]。 您可以透過 [**類型**] 欄位上的 [多重選取支援]，從單一支援案例要求標準和點配額類型。 深入瞭解此[頁面](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)上的**增加位置配額限制**

   ![SKU 系列](./media/resource-manager-core-quotas-request/1-8.png)

9. 選取需要增加的**SKU 系列**

   ![SKU 系列](./media/resource-manager-core-quotas-request/1-9.png)

10. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請取消核取 [SKU 系列] 下拉式方塊中的 SKU，或按一下 [捨棄 "x"] 圖示。 

   ![新限制](./media/resource-manager-core-quotas-request/1-10.png)

11. 若要要求多個位置的配額，您可以從下拉式選單查看另一個**位置**，然後選取適當的 VM 類型。 此步驟會預先載入針對新位置選取的先前**位置**的 SKU 系列，您可以直接輸入您想要的新限制。

   ![多個位置](./media/resource-manager-core-quotas-request/1-11.png)
   
12. 為每個 SKU 系列輸入所需的配額之後，請按一下 [配額詳細資料] 面板上的 [**儲存並繼續**]，以繼續支援要求的建立。

## <a name="request-standard-vcpu-quota-increase-per-vm-series-at-subscription-level-using-usages--quota-blade"></a>使用使用量 + 配額分頁在訂用帳戶層級的每個 VM 系列要求標準 vCPU 配額增加

請遵循下列指示，以使用 Azure 入口網站中提供的 Azure [使用量 + 配額] 分頁來建立支援要求。

您也可以透過單一支援案例來**要求多個區域的配額**。 如需詳細資訊，請參閱下面的步驟10

1. 從 https://portal.azure.com 選取 [訂用帳戶]。

   ![訂閱](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 選取需要增加配額的訂用帳戶。

   ![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 選取 [使用量 + 配額]

   ![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角，選取 [要求增加配額]。

   ![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5. 選取 [**計算-VM （核心-個 vcpu）訂用帳戶限制] 會增加**為報價類型。 

   ![填寫表單](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. 在 [**配額詳細資料**] 面板中，選取 [部署模型]，然後選取位置。

   ![配額問題刀鋒視窗](./media/resource-manager-core-quotas-request/1-1-6.png)

7. 針對選取的位置，選取 [**類型**值] 為 [**標準**]。 您可以透過 [**類型**] 欄位上的 [多重選取支援]，從單一支援案例要求標準和低優先順序的配額類型。 深入瞭解此[頁面](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)上的**增加點個 vcpu 限制**。

   ![選取的 SKU 系列](./media/resource-manager-core-quotas-request/1-1-7.png)
   
   
8. 選取需要增加的**SKU 系列**

   ![選取的 SKU 系列](./media/resource-manager-core-quotas-request/1-1-8.png)

9. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請取消核取 [SKU 系列] 下拉式方塊中的 SKU，或按一下 [捨棄 "x"] 圖示。 

   ![SKU 新配額要求](./media/resource-manager-core-quotas-request/1-1-9.png)
   

10. 若要要求多個位置的配額，您可以從下拉式選單查看另一個**位置**，然後選取適當的 VM 類型。 此步驟會預先載入針對新位置選取的先前**位置**的 SKU 系列，您可以直接輸入您想要的新限制。
   
    ![SKU 新配額要求](./media/resource-manager-core-quotas-request/1-1-10.png)
 
