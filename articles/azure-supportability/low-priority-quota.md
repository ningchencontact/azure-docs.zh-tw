---
title: 點配額 |Microsoft Docs
description: 找出配額要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 11/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 09c9d7940314b691e6351353e6a0076510fdcb13
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850562"
---
# <a name="spot-quota-limit-increase-for-all-vm-series"></a>點配額：增加所有 VM 系列的限制

「找出 Vm」提供不同的 Azure 使用量模型，並以較低的成本進行交易，讓 Azure 在隨用隨付或保留的 VM 實例部署時，視需要移除 VM。 [在這裡](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot)深入瞭解如何找出 vm。

Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額。 **隨用隨付 vm 和保留的 Vm 實例**會使用標準配額。 **找出 vm**會使用點配額。 

對於「**點」配額**類型，Resource Manager vCPU 配額會在所有可用的 VM 系列中強制執行，做為單一區域限制。

每當有新的點 VM 部署時，所有點 VM 實例的新的和現有個 vcpu 使用量總和，都不得超過已核准的點 vCPU 配額限制。 如果超過了位置配額，將不允許點 VM 部署。 您可以要求增加 Azure 入口網站的位置個 vcpu 配額限制。 

在 [虛擬機器 vCPU 配額] 頁面和 [Azure 訂用帳戶和服務限制] 頁面上深入瞭解標準 vCPU 配額。 深入瞭解如何增加此[頁面](https://docs.microsoft.com/azure/azure-supportability/regional-quota-requests)上的標準配額的地區 vCPU 限制。

您現在可以透過入口網站中的 [說明 **+ 支援**] 分頁或 [**使用方式 + 配額**] 分頁，要求增加**所有 VM 系列的「位置配額限制**」。

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-the-help--support-blade"></a>使用 [說明 + 支援] 分頁，針對每個訂用帳戶的所有 VM 系列增加要求點配額限制

請依照下列指示，透過 Azure 入口網站中提供的 Azure [說明 + 支援] 分頁來建立支援要求。

您也可以透過單一支援案例來**要求多個區域的配額**。 如需詳細資訊，請參閱下面的步驟10。 


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
   
7.  在 [**配額詳細資料**] 窗格中，選取 [**部署模型**] 並選取**位置**。

![提供詳細資料](./media/resource-manager-core-quotas-request/3-7.png)

8. 針對選取的位置，選取 [**類型**值] 做為 [**點**]。 您可以透過 [**類型**] 欄位上的 [多重選取支援]，從單一支援案例要求標準和點配額類型。 深入瞭解如何在此[頁面](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)上**增加每個 VM 的標準配額一系列**。

![提供詳細資料](./media/resource-manager-core-quotas-request/3-8.png)

9.  輸入您想要在訂用帳戶上的新限制。 
 
 ![提供詳細資料](./media/resource-manager-core-quotas-request/3-9.png)

10. 若要要求多個位置的配額，您可以從下拉式選單查看另一個位置，然後選取適當的 VM 類型。 然後，您可以輸入您想要的新限制。

![提供詳細資料](./media/resource-manager-core-quotas-request/3-10.png)

11. 輸入所需的配額之後，請按一下 [配額詳細資料] 面板上的 [**儲存並繼續**]，以繼續支援要求的建立。

## <a name="request-spot-quota-limit-increase-for-all-vm-series-per-subscription-using-usages--quota-blade"></a>針對每個訂用帳戶的所有 VM 系列，使用使用量 + 配額分頁增加要求點配額限制

請遵循下列指示，以使用 Azure 入口網站中提供的 Azure [使用量 + 配額] 分頁來建立支援要求。

您也可以透過單一支援案例來**要求多個區域的配額**。 如需詳細資訊，請參閱下面的步驟9。 

1.  從 https://portal.azure.com 選取 [訂用帳戶]。

 ![訂閱](./media/resource-manager-core-quotas-request/subscriptions.png)

2.  選取需要增加配額的訂用帳戶。

 ![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3.  選取 [使用量 + 配額]。

 ![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4.  在右上角，選取 [**要求增加]。**

   ![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5.  選取 [**計算-VM （核心-個 vcpu）訂用帳戶限制] 會增加**為報價類型。

  ![填寫表單](./media/resource-manager-core-quotas-request/select-quota-type.png)

6.  在 [**配額詳細資料**] 面板中，選取 [部署模型]，然後選取位置。

  ![填寫表單](./media/resource-manager-core-quotas-request/3-2-6.png)
 
7.  針對選取的位置，選取 [**類型**值] 做為 [**點]。** 您可以透過 [**類型**] 欄位上的 [多重選取支援]，從單一支援案例要求標準和點配額類型。 深入瞭解如何在此[頁面](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests)上**增加每個 VM 的標準配額一系列**。

  ![填寫表單](./media/resource-manager-core-quotas-request/3-2-7.png)
 
8.  輸入您想要在訂用帳戶上的新限制。

  ![填寫表單](./media/resource-manager-core-quotas-request/3-2-8.png)
 
9.  若要要求多個位置的配額，您可以從下拉式選單查看另一個**位置**，然後選取適當的 VM 類型。 然後，您可以輸入您想要的新限制。

  ![填寫表單](./media/resource-manager-core-quotas-request/3-2-9.png)
 
10. 輸入所需的配額之後，請按一下 [配額詳細資料] 面板上的 [**儲存並繼續**]，以繼續支援要求的建立。


