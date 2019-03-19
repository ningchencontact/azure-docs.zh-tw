---
title: Azure SaaS 應用程式供應項目試用產品設定 | Microsoft Docs
description: 在 Azure Marketplace 上設定 SaaS 應用程式供應項目的試用產品。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: e97e79ef3077431d8fc627cd634a67b0a01b6f8e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993102"
---
# <a name="saas-application-test-drive-tab"></a>SaaS 應用程式試用產品索引標籤

使用 [試用產品] 索引標籤，為您的客戶提供試用體驗。

## <a name="test-drive-benefits"></a>試用產品的權益

為您的客戶建立試用體驗是確定他們能有信心購買的最佳做法。 在可用的試用選項中，試用產品是獲得高品質潛在客戶及提高那些潛在客戶轉換為付費客戶之轉換率最有效的方式。

試用產品可讓客戶以自我引導方式實際操作您試用產品的主要功能與權益，而且會以真實世界實作案例來示範。

## <a name="how-a-test-drive-works"></a>試用產品如何運作

潛在客戶在 Marketplace 上搜尋並探索您的應用程式。 客戶登入並同意使用規定。 此時，客戶收到您預先設定的環境，並在固定的數小時內試用，而您也得到可進行後續追蹤的最適合潛在客戶。 如需詳細資訊，請參閱[什麼是試用產品？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="publishing-steps"></a>發佈步驟

新增試用產品的主要發行步驟是：

1. 定義您的試用產品案例
2. 建置和/或修改您的 Resource Manager 範本
3. 建立您的試用產品逐步手冊
4. 重新發行您的供應項目

## <a name="setting-up-a-test-drive"></a>設定試用產品

我們根據產品類型、案例和您所在的市集，提供四種不同類型的試用產品以供使用。

|  **類型**          |  **說明**  |  **設定指示**  |
|  ---------------   |  ---------------  |  ---------------  |
|     Azure Resource Manager               |    Azure Resource Manager 試用產品是一個部署範本，其中包含組成發行者所建置之解決方案的所有 Azure 資源。 適用於此類型試用產品的產品為僅使用 Azure 資源的產品。               |       [Azure Resource Manager 試用產品](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)            |
|       裝載             |       「裝載的試用產品」透過由 Microsoft 提供服務裝載環境免除了設定的複雜性，而且它會維護執行試用產品使用者佈建及取消佈建的服務。             |         [已裝載的試用產品](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive)          |
|      邏輯應用程式              |       邏輯應用程式試用產品是一個部署範本，其目的是包含所有複雜解決方案架構。 所有 Dynamics 應用程式或自訂產品，都應該使用此類型的試用產品。            |      [邏輯應用程式試用產品](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)             |
|       Power BI             |         Power BI 試用產品會由自訂建置儀表板的內嵌連結所組成。 任何要示範互動式 Power BI 視覺效果的產品，都應該使用此類型的試用產品。 您只需要上傳內嵌的 Power BI URL。          |        [Power BI 試用產品](#Power-BI-test-drive)           |

### <a name="power-bi-test-drive"></a>Power BI 試用產品

使用下列步驟來設定試用產品。

1. 在 [新增供應項目] 底下，選取 [試用產品]。
2. 在 [試用產品] 上，選取 [是]。

   ![啟用試用產品](./media/saas-enable-test-drive.png)

   當您啟用試用產品時，將會看到 [詳細資料] 和 [技術設定]表單，如下一個螢幕擷取畫面所示。

   ![試用產品設定表單](./media/saas-test-drive-yes.png)

3. 在 [詳細資料] 底下，提供下列欄位的資訊：
  
   - 描述：描述您的試用產品，以及使用者如何使用它。 您可以使用基本 HTML 標記來設定這個描述的格式。
   - 使用者手冊：上傳使用者手冊文件，讓您的客戶可以在採用試用產品時使用它們。 此手冊必須是 .pdf 檔案。
   - 試用產品示範影片 (選擇性)：您可以為客戶提供一段影片 (YouTube 或 Vimeo)，讓他們在採用試用產品之前先行觀賞。 提供影片的 URL。

4. 在 [技術設定] 底下，提供下列欄位的資訊：

   - 試用產品的類型：從下拉式清單中選取 [Power BI]。
   - Power BI 儀表板的連結：提供儀表板的連結。

5. 當您完成試用產品設定之後，選取 [儲存]。


## <a name="next-steps"></a>後續步驟

[店面詳細資料索引標籤](./cpp-storefront-tab.md)