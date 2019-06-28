---
title: Azure 區域的配額增加要求 |Microsoft Docs
description: 地區配額增加要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310655"
---
# <a name="total-regional-vcpu-limit-increase"></a>增加的區域總 vCPU 限制 

每個訂用帳戶，每個區域中的兩個層級強制執行的虛擬機器和虛擬機器擴展集的 resource Manager vCPU 配額。 

第一層是**總區域 Vcpu 限制**（跨所有 VM 系列），第二個層，而且**每個 VM 系列 Vcpu 限制**（例如 D 系列 Vcpu)。 每當新的 VM 時要部署新的和現有的 Vcpu 的 VM 系列的使用方式的總和不得超過核准該特定的 VM 系列的 vCPU 配額。 此外，跨所有 VM 系列部署的總新的和現有的 vCPU 計數不應該超過核准訂用帳戶的總區域 Vcpu 配額。 如果超過這些配額，將不允許 VM 部署。
您可以從 Azure 入口網站要求增加 VM 系列的 Vcpu 配額限制。 VM 系列配額增加會自動增加總區域 Vcpu 限制，以相同數量。 

建立新的訂用帳戶時，可能不等於所有個別的 VM 系列的預設 vCPU 配額總和預設區域 Vcpu 總計。 這會導致訂用帳戶具有足夠的配額為每個個別的 VM 系列，您想要部署，但所有部署的總區域 vcpu 的配額不足。 在此情況下，您必須提交要求，以明確地增加總區域 Vcpu 限制。 區域 Vcpu 總計限制不能已核准的配額的總和超過跨區域的所有 VM 系列。

深入了解配額上[虛擬機器 vCPU 配額頁面](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)並[Azure 訂用帳戶和服務限制](https://aka.ms/quotalimits)頁面。 

您現在可以要求透過增加**說明 + 支援**刀鋒視窗或**使用量 + 配額**入口網站刀鋒視窗。 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>要求在訂用帳戶層級使用的總區域 Vcpu 配額增加**說明 + 支援**刀鋒視窗

請遵循下列指示來建立支援要求，透過 Azure 的 [說明 + 支援] 刀鋒視窗中使用 Azure 入口網站中。 

1. 從 https://portal.azure.com ，選取**說明 + 支援**。

![說明 + 支援](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  選取 [新增支援要求]  。 

![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 [問題類型] 下拉式清單中，選擇**服務和訂用帳戶的限制 （配額）** 。

![問題類型下拉式清單](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 選取需要增加配額的訂用帳戶。

![選取的訂用帳戶 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 選取 **其他要求**中**配額類型**下拉式清單。

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. 在 **詳細資料**窗格中，提供額外的資訊，根據下列範例中，以協助處理您的要求，並繼續進行案例建立。 
    1.  **部署模型**– 指定 「 資源管理員 」
    2.  **要求的地區**– 指定您所需的區域例如美國東部 2
    3.  **新的限制值**– 指定新的區域限制。 這不應該超過核准個別的 SKU 系列，此訂用帳戶配額的總和

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>要求在訂用帳戶層級使用的總區域 Vcpu 配額增加**使用方式 + 配額**刀鋒視窗

請依照下列指示來建立支援要求，透過 Azure 的 [使用量 + 配額] 使用 Azure 入口網站中可用的刀鋒視窗。 

1. 從 https://portal.azure.com 選取 [訂用帳戶]  。

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 選取需要增加配額的訂用帳戶。

![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 選取 [使用量 + 配額] 

![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角，選取 [要求增加配額]  。

![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5. 選取 **其他要求**中**配額類型**下拉式清單。

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. 在 **詳細資料**窗格中，提供額外的資訊，根據下列範例中，以協助處理您的要求，並繼續進行案例建立。 
    1.  **部署模型**– 指定 「 資源管理員 」
    2.  **要求的地區**– 指定您所需的區域例如美國東部 2
    3.  **新的限制值**– 指定新的區域限制。 這不應該超過核准個別的 SKU 系列，此訂用帳戶配額的總和

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



