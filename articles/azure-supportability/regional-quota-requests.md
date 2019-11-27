---
title: Azure 區域配額增加要求 |Microsoft Docs
description: 區域配額增加要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 6c2d7dc64bca00ce664dc470e7c4405d69b49779
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2019
ms.locfileid: "74531529"
---
# <a name="standard-quota-regional-vcpu-limit-increase"></a>標準配額：區域 vCPU 限制增加 

Resource Manager 針對虛擬機器支援兩種類型的 vCPU 配額。 **隨用隨付 vm**和**保留的 vm 實例**會使用標準配額。 **低優先順序的 vm**會使用低優先順序的配額。 

隨用隨付和保留的 VM 實例的標準 vCPU 配額會針對每個區域中的每個訂用帳戶，在兩個層級強制執行。
 
第一層是**總區域個 vcpu 限制**（跨所有 vm 系列），而第二層是**每個 vm 系列個 vcpu 限制**（例如 D 系列個 vcpu）。 每當部署新的 VM 時，該 VM 系列新的和現有個 vcpu 使用量的總和，不得超過該特定 VM 系列所核准的 vCPU 配額。 此外，在所有 VM 系列上部署的新的和現有的 vCPU 計數，不應超過訂用帳戶所核准的區域個 vcpu 配額總計。 如果超過這些配額，將不允許 VM 部署。 您可以從 Azure 入口網站為 VM 系列要求增加個 vcpu 配額限制。 VM 系列配額的增加會自動增加相同數量的總區域個 vcpu 限制。

建立新的訂用帳戶時，預設的 [區域個 vcpu 總計] 可能不等於所有個別 VM 系列的預設 vCPU 配額總和。 這可能會導致您想要部署的每個個別 VM 系列的訂用帳戶具有足夠的配額，但不會有足夠的配額可用於所有部署的總區域個 vcpu。 在此情況下，您將需要提交要求，以明確地增加區域個 vcpu 限制的總計。 區域個 vcpu 限制總計不得超過該區域所有 VM 系列的核准配額總和。

在 [[虛擬機器 vCPU 配額] 頁面](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和 [Azure 訂用帳戶[和服務限制](https://aka.ms/quotalimits)] 頁面上深入瞭解標準 vCPU 配額。

[在這裡](https://docs.microsoft.com/azure/azure-supportability/low-priority-quota)深入瞭解如何**增加低優先順序的 VM vCPU 限制**。

您可以透過入口網站中的 [說明 **+ 支援**] 分頁或 [**使用方式 + 配額**] 分頁，要求增加**標準 VM 的總區域 vCPU 限制**。

## <a name="request-standard-quota-regional-vcpu-limit-increase-at-subscription-level-using-the-help--support-blade"></a>使用 [說明 + 支援] 分頁來要求在訂用帳戶層級增加的標準配額區域 vCPU 限制

請依照下列指示，透過 Azure 入口網站中提供的 Azure [說明 + 支援] 分頁來建立支援要求。 

1. 從 https://portal.azure.com選取 [說明 **+ 支援**]。

![說明 + 支援](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  選取 [新增支援要求]。 

![新的支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 [問題類型] 下拉式選單中，選擇 [**服務與訂用帳戶限制（配額）** ]。

![問題類型下拉式](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 選取需要增加配額的訂用帳戶。

![選取訂用帳戶 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 在 [**配額類型**] 下拉式選單中選取 [**其他要求**]。

![配額類型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. 在**詳細資料**窗格中，依據下列範例提供其他資訊，以協助處理您的要求並繼續建立案例。 
    1.  **部署模型**–指定 ' Resource Manager '
    2.  **要求的區域**-指定您所需的區域，例如美國東部2
    3.  **新的限制值**-指定新的區域限制。 這不應該超過此訂用帳戶的個別 SKU 系列已核准配額的總和

![配額詳細資料](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>使用 [使用方式 **+ 配額**] 分頁來要求訂用帳戶層級的總區域個 vcpu 配額增加

請遵循下列指示，以使用 Azure 入口網站中提供的 Azure [使用量 + 配額] 分頁來建立支援要求。 

1. 從 https://portal.azure.com 選取 [訂用帳戶]。

![訂用帳戶](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 選取需要增加配額的訂用帳戶。

![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 選取 [使用量 + 配額]

![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角，選取 [要求增加配額]。

![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5. 在 [**配額類型**] 下拉式選單中選取 [**其他要求**]。

![配額類型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. 在**詳細資料**窗格中，依據下列範例提供其他資訊，以協助處理您的要求並繼續建立案例。 
    1.  **部署模型**–指定 ' Resource Manager '
    2.  **要求的區域**-指定您所需的區域，例如美國東部2
    3.  **新的限制值**-指定新的區域限制。 這不應該超過此訂用帳戶的個別 SKU 系列已核准配額的總和

![配額詳細資料](./media/resource-manager-core-quotas-request/regional-details.png)



