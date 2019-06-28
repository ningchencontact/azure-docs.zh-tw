---
title: 網路限制提高 |Microsoft Docs
description: 增加網路限制
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297273"
---
# <a name="networking-limit-increase"></a>增加網路限制

若要檢視您目前的網路使用量和配額，您可以瀏覽**使用方式 + 配額**刀鋒視窗中，在 Azure 入口網站中的。 您也可以使用使用情況[CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)， [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0)或[網路使用情況 API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage)若要檢視您的網路使用量和限制。

您可以要求透過增加**說明 + 支援**刀鋒視窗或**使用量 + 配額**入口網站刀鋒視窗。

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>要求在訂用帳戶層級使用的網路配額增加**說明 + 支援**刀鋒視窗

請遵循下列指示來建立支援要求，透過 Azure 的 [說明 + 支援] 刀鋒視窗中使用 Azure 入口網站中。 

1. 從 https://portal.azure.com ，選取**說明 + 支援**。

    ![說明 + 支援](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  選取 [新增支援要求]  。 

    ![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 [問題類型] 下拉式清單中，選擇**服務和訂用帳戶的限制 （配額）** 。

    ![問題類型下拉式清單](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 選取需要增加配額的訂用帳戶。

    ![選取的訂用帳戶 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 選取  **Networking**中**配額類型**下拉式清單。 

    ![選取 配額類型](./media/networking-quota-request/select-quota-type-network.png)

6. 在 **問題的詳細資料**，按一下您的要求提供其他資訊來協助處理程序**提供詳細資料，** 。

    ![提供詳細資料](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 [**配額的詳細資料**] 面板中，選取部署模型、 位置和您想要要求增加的資源。

    ![配額的詳細資料 DM](./media/networking-quota-request/quota-details-network.png)

8.  輸入您想要對訂用帳戶採取的新限制。 若要移除一行，取消核取 從資源的下拉式清單中的資源，或按一下捨棄 x 圖示。 輸入所需的配額為每個資源之後，按一下**儲存並繼續**上配額詳細資料窗格中，若要繼續建立支援要求。

    ![新的限制](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>要求在訂用帳戶層級使用的網路配額增加**使用方式 + 配額**刀鋒視窗

請依照下列指示來建立支援要求，透過 Azure 的 [使用量 + 配額] 使用 Azure 入口網站中可用的刀鋒視窗。 

1. 從 https://portal.azure.com 選取 [訂用帳戶]  。

    ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 選取需要增加配額的訂用帳戶。

    ![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 選取 [使用量 + 配額] 

    ![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角，選取 [要求增加配額]  。

    ![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5. 遵循的步驟，開始從步驟 3*訂用帳戶層級增加的要求網路配額*一節使用**說明 + 支援**刀鋒視窗中的區段

## <a name="about-networking-limits"></a>關於網路服務限制

若要深入了解網路限制，請參閱[網路 > 一節](../azure-subscription-service-limits.md#networking-limits)限制頁面或我們的網路限制常見問題集