---
title: Azure 每個 VM vCPU 配額增加要求 |Microsoft Docs
description: 每個 VM vCPU 配額增加要求數
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9caf82d2fa4683e845d23b5496f9e7b8f9682ec7
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234833"
---
# <a name="vm-series-vcpu-limit-increase"></a>增加的 VM 系列 vCPU 限制

針對每個區域中的每個訂用帳戶, 會在兩個層級強制執行虛擬機器和虛擬機器擴展集的 Resource Manager vCPU 配額。 

第一層是**總區域個 vcpu 限制**(跨所有 vm 系列), 而第二層是**每個 vm 系列個 vcpu 限制**(例如 D 系列個 vcpu)。 每當部署新的 VM 時, 該 VM 系列新的和現有個 vcpu 使用量的總和, 不得超過該特定 VM 系列所核准的 vCPU 配額。 此外, 在所有 VM 系列上部署的新的和現有的 vCPU 計數, 不應超過訂用帳戶所核准的區域個 vcpu 配額總計。 如果超過這些配額，將不允許 VM 部署。
您可以從 Azure 入口網站為 VM 系列要求增加個 vcpu 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域個 vcpu 限制。 

深入瞭解 [[虛擬機器 vCPU 配額] 頁面](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和 [ [Azure 訂用帳戶和服務限制](https://aka.ms/quotalimits)] 頁面上的配額。 

您現在可以在入口網站中透過 [說明 **+ 支援**] 分頁或 [**使用方式 + 配額**] 分頁要求增加。 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>使用 [說明 **+ 支援**] 分頁, 在訂用帳戶層級增加每個 VM 系列 vCPU 配額的要求

請依照下列指示, 透過 Azure 入口網站中提供的 Azure [說明 + 支援] 分頁來建立支援要求。 

1. 從 https://portal.azure.com 選取 [說明 **+ 支援**]。

   ![說明 + 支援](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  選取 [新增支援要求]  。 

     ![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 [問題類型] 下拉式清單中, 選擇 [**服務與訂用帳戶限制 (配額)** ]。

   ![問題類型下拉式清單](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 選取需要增加配額的訂用帳戶。

   ![選取訂用帳戶 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 在 [**配額類型**] 下拉式清單中, 選取 **[計算-VM (核心-個 vcpu) 訂用帳戶限制增加**]。 

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. 在 [**問題詳細資料**] 中, 按一下 [**提供詳細資料**], 提供其他資訊來協助處理您的要求。

   ![提供詳細資料](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 [**配額詳細資料**] 面板中, 選取 [部署模型], 然後選取位置。

   ![配額詳細資料 DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. 選取需要增加的**SKU 系列**。 

   ![SKU 系列](./media/resource-manager-core-quotas-request/sku-family.png)

9. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請從 SKU 系列下拉式清單中取消核取 SKU，或按一下捨棄 [x] 圖示。 為每個 SKU 系列輸入所需的配額之後, 請按一下 [配額詳細資料] 面板上的 [**儲存並繼續**], 以繼續支援要求的建立。

   ![新限制](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>使用**使用量 + 配額**分頁在訂用帳戶層級增加的每一 VM 系列 vCPU 配額的要求數

請遵循下列指示, 以使用 Azure 入口網站中提供的 Azure [使用量 + 配額] 分頁來建立支援要求。 

1. 從 https://portal.azure.com 選取 [訂用帳戶]  。

   ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 選取需要增加配額的訂用帳戶。

   ![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 選取 [使用量 + 配額] 

   ![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角，選取 [要求增加配額]  。

   ![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5. 選取 [**計算-VM (核心-個 vcpu) 訂用帳戶限制] 會增加**為報價類型。 

   ![填寫表單](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. 在 [**配額詳細資料**] 面板中, 選取 [部署模型], 然後選取位置。

   ![配額問題刀鋒視窗](./media/resource-manager-core-quotas-request/quota-details.png)

7. 選取需要增加的**SKU 系列**。

   ![選取的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family.png)

8. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請從 SKU 系列下拉式清單中取消核取 SKU，或按一下捨棄 [x] 圖示。 為每個 SKU 系列輸入所需的配額之後, 請按一下 [問題步驟] 頁面上的 [**儲存並繼續**], 以繼續支援要求的建立。

   ![SKU 新配額要求](./media/resource-manager-core-quotas-request/new-limits.png)
 
