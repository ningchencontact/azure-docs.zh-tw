---
title: 了解您的 Azure 外部服務費用 | Microsoft Docs
description: 了解 Azure 中外部服務 (先前稱為 Marketplace) 費用的計費方式。
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 90753c6046425b60fda04fa99b2952e706d9c0e5
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311884"
---
# <a name="understand-your-azure-external-services-charges"></a>了解您的 Azure 外部服務費用
在 Azure marketplace 中的第三方軟體廠商所發佈的外部服務。 比方說，SendGrid 會是您可以在 Azure 中，購買，但不是會由 Microsoft 所發佈的外部服務。 某些 Microsoft 產品會透過 Azure marketplace，太銷售。

## <a name="how-external-services-are-billed"></a>如何對外部服務計費

- 如果您有[Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)，第三方服務計費方式為您的 Azure 服務的其餘部分。
- 如果您沒有 Microsoft 客戶合約，您的外部服務會從您的 Azure 服務分開計費。
- 每項外部服務都有各自的計費模式。 某些服務計費以隨用隨付的方式，而其他人有固定的每月費用。
- 外部服務不適用於每個月的免費信用額度。 如果您使用的 Azure 訂用帳戶，其中包含[免費信用額度](https://azure.microsoft.com/pricing/spending-limits/)，它們無法從外部服務套用費用。 當您佈建新的外部服務或資源時，系統會顯示警告︰

    ![Marketplace 購買警告](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/): 

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.
   
    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.
   
    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.
   
    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.
   
    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-azure-marketplace-invoices"></a>檢視和下載 Azure marketplace 發票

如果您有[Microsoft 客戶合約](#check-access-to-a-microsoft-customer-agreement)，您的第三方費用會在您的 Azure 費用為相同的發票上。 了解如何[檢視並下載您的 Azure 發票](billing-download-azure-invoice.md)從 Azure 入口網站，以查看您的第三方費用。

如果您沒有 Microsoft 客戶合約，您會有不同的第三方費用的發票。 您可以檢視，並從 Azure 入口網站下載您的 Azure Marketplace 發票，依照下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 針對 [成本管理 + 帳單]  進行搜尋。
1. 在左側功能表中，選取**發票**。
1. 按一下 [ **Azure Marketplace 和保留項目**] 索引標籤。![Azure marketplace 和保留項目 索引標籤的圖片](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. 在訂用帳戶 下拉式清單，選取包含您想要查看的發票的外部服務的訂用帳戶。

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>檢視 Enterprise 合約 (EA) 客戶的外部服務消費

EA 客戶可以在 EA 入口網站看到外部服務消費並下載報告。 請參閱 [EA 客戶的 Azure Marketplace](https://ea.azure.com/helpdocs/azureMarketplace) 以便開始使用。

## <a name="manage-payment-methods-for-external-service-orders"></a>管理外部服務訂單的付款方法

當購買外部服務，您會選擇資源的 Azure 訂用帳戶。 選取的 Azure 訂用帳戶的付款方法會變成外部服務的付款方法。 若要變更的外部服務的付款方法，您必須[變更 Azure 訂用帳戶的付款方式](billing-how-to-change-credit-card.md)繫結至該外部的服務。 您可以找出哪個外部服務訂單繫結的訂閱依照下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下 **的所有資源**左的導覽功能表中。
     ![所有資源 功能表項目的螢幕擷取畫面](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. 搜尋您的外部服務。
1. 尋找在訂用帳戶名稱**訂用帳戶**資料行。
    ![資源的訂用帳戶名稱的螢幕擷取畫面](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. 按一下訂用帳戶名稱和[更新使用中付款方式](billing-how-to-change-credit-card.md)。
 
<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)
   
    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage
   
    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.
   
    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.
   
    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>取消外部服務訂單
如果想要取消外部服務訂單，請在 [Azure 入口網站](https://portal.azure.com)中刪除該資源。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 按一下 **的所有資源**左的導覽功能表中。
    ![所有資源 功能表項目的螢幕擷取畫面](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. 搜尋您的外部服務。
1. 請檢查您想要刪除的資源旁的方塊。
1. 選取 **刪除**命令列中。
    ![[刪除] 按鈕的螢幕擷取畫面](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. 型別 *'Yes'* 的確認刀鋒視窗中。
    ![刪除資源](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. 按一下 [刪除]  。



## <a name="check-access-to-a-microsoft-customer-agreement"></a>檢查 Microsoft 客戶合約的存取權
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有任何疑問或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

