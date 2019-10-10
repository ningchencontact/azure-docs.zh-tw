---
title: Azure 傳統部署模型 |Microsoft Docs
description: Azure 傳統部署模型
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: d803cc0dfa4a266f79b522207a8cf0f70e06cf3d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248727"
---
# <a name="classic-deployment-model"></a>傳統部署模型

傳統部署模型是較舊的世代 Azure 部署模型。 它會強制執行虛擬機器和虛擬機器擴展集的全域 vCPU 配額限制。 不再建議使用傳統部署模型，而且現在已由 Resource Manager 模型取代。 

若要深入瞭解這兩種部署模型以及使用 Resource Manager 的優點，請參閱[Resource Manager 和傳統部署](../azure-resource-manager/resource-manager-deployment-model.md)頁面。
 
建立新的訂用帳戶時，會指派個 vcpu 的預設配額。 每當使用傳統部署模型部署新的 VM 時，所有區域的新和現有 vCPU 使用量總和，不得超過針對傳統部署模型所核准的 vCPU 配額。
 
深入瞭解 Azure 訂用帳戶[和服務限制](https://aka.ms/quotalimits)頁面上的配額。

您可以透過入口網站中的 [說明 + 支援] 分頁或 [使用方式 + 配額] 分頁，要求增加傳統部署模型的 vCPU 配額限制。

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>使用 [說明 **+ 支援**] 分頁，在訂用帳戶層級增加每個 VM 系列 vCPU 配額的要求

請依照下列指示，透過 Azure 入口網站中提供的 Azure [說明 + 支援] 分頁來建立支援要求。 

1. 從 https://portal.azure.com ，選取 [說明 **+ 支援**]。

   ![說明 + 支援](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  選取 [新增支援要求]。 

      ![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 [問題類型] 下拉式清單中，選擇 [**服務與訂用帳戶限制（配額）** ]。

   ![問題類型下拉式清單](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 選取需要增加配額的訂用帳戶。

   ![選取訂用帳戶 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 在 [**配額類型**] 下拉式清單中，選取 **[計算-VM （核心-個 vcpu）訂用帳戶限制增加**]。 

   ![選取配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. 在 [**問題詳細資料**] 中，按一下 [**提供詳細資料**]，提供其他資訊來協助處理您的要求。

   ![提供詳細資料](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 [**配額詳細資料**] 面板中，選取 [傳統]，然後選取位置。

   ![配額詳細資料 DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. 選取需要增加的**SKU 系列**。 

   ![SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請從 SKU 系列下拉式清單中取消核取 SKU，或按一下捨棄 [x] 圖示。 為每個 SKU 系列輸入所需的配額之後，請按一下 [配額詳細資料] 面板上的 [**儲存並繼續**]，以繼續支援要求的建立。

   ![新限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>使用**使用量 + 配額**分頁在訂用帳戶層級增加的每一 VM 系列 vCPU 配額的要求數

請遵循下列指示，以使用 Azure 入口網站中提供的 Azure [使用量 + 配額] 分頁來建立支援要求。 

1. 從 https://portal.azure.com 選取 [訂用帳戶]。

   ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 選取需要增加配額的訂用帳戶。

   ![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 選取 [使用量 + 配額]

   ![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角，選取 [要求增加配額]。

   ![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5. 選取 [**計算-VM （核心-個 vcpu）訂用帳戶限制] 會增加**為報價類型。 

   ![填寫表單](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. 在 [**問題詳細資料**] 中，按一下 [**提供詳細資料**]，提供其他資訊來協助處理您的要求。

   ![提供詳細資料](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 [**配額詳細資料**] 面板中，選取 [傳統]，然後選取位置。

   ![配額詳細資料 DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. 選取需要增加的**SKU 系列**。 

   ![SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請從 SKU 系列下拉式清單中取消核取 SKU，或按一下捨棄 [x] 圖示。 為每個 SKU 系列輸入所需的配額之後，請按一下 [配額詳細資料] 面板上的 [**儲存並繼續**]，以繼續支援要求的建立。

   ![新限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

