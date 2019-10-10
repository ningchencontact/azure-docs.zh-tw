---
title: 網路限制增加 |Microsoft Docs
description: 增加網路限制
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 278e9ff68fa20a0a99a6447bb4cf7ac7fddbfb7b
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249104"
---
# <a name="networking-limit-increase"></a>增加網路限制

若要查看目前的網路使用量和配額，您可以造訪 Azure 入口網站中的 [**使用方式 + 配額**] 分頁。 您也可以使用使用方式[CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)、 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0)或[網路使用方式 API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage)來查看您的網路使用量和限制。

您可以透過入口網站中的 [說明 **+ 支援**] 分頁或 [**使用方式 + 配額**] 分頁來要求增加。

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>使用 [說明 **+ 支援**] 分頁，在訂用帳戶層級增加要求網路配額

請依照下列指示，透過 Azure 入口網站中提供的 Azure [說明 + 支援] 分頁來建立支援要求。 

1. 從 https://portal.azure.com ，選取 [說明 **+ 支援**]。

    ![說明 + 支援](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  選取 [新增支援要求]。 

    ![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 [問題類型] 下拉式清單中，選擇 [**服務與訂用帳戶限制（配額）** ]。

    ![問題類型下拉式清單](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 選取需要增加配額的訂用帳戶。

    ![選取訂用帳戶 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 在 [**配額類型**] 下拉式清單中選取 [**網路**]。 

    ![選取配額類型](./media/networking-quota-request/select-quota-type-network.png)

6. 在 [**問題詳細資料**] 中，按一下 [**提供詳細資料**]，提供其他資訊來協助處理您的要求。

    ![提供詳細資料](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 [**配額詳細資料**] 面板中，選取 [部署模型]、[位置] 和您想要要求增加的資源。

    ![配額詳細資料 DM](./media/networking-quota-request/quota-details-network.png)

8.  輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請從 [資源] 下拉式清單中取消選取資源，或按一下 [捨棄 "x"] 圖示。 輸入每項資源所需的配額之後，請按一下 [配額詳細資料] 面板上的 [**儲存並繼續**]，以繼續支援要求的建立。

    ![新限制](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>使用**使用量 + 配額**分頁在訂用帳戶層級增加要求網路配額

請遵循下列指示，以使用 Azure 入口網站中提供的 Azure [使用量 + 配額] 分頁來建立支援要求。 

1. 從 https://portal.azure.com 選取 [訂用帳戶]。

    ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 選取需要增加配額的訂用帳戶。

    ![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 選取 [使用量 + 配額]

    ![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角，選取 [要求增加配額]。

    ![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5. 依照使用 [說明 **+ 支援**] 分頁一節中的步驟 # 3，從訂用帳戶*層級增加的要求網路配額*一節中的步驟開始。

## <a name="about-networking-limits"></a>關於網路限制

若要深入瞭解網路限制，請參閱限制頁面的[網路功能區段](../azure-subscription-service-limits.md#networking-limits)或我們的網路限制常見問題