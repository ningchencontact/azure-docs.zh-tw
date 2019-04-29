---
title: 更新 Power BI 應用程式供應項目-Azure Marketplace |Microsoft Docs
description: 在 Power BI 應用程式發佈到 Microsoft AppSource Marketplace 之後更新它。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 953c53b4ef2244b3444abb745f52e125306468f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725514"
---
# <a name="update-a-power-bi-app-offer"></a>更新 Power BI 應用程式供應項目

本文說明如何更新將 Microsoft Power BI 應用程式供應項目[Cloud Partner 入口網站](https://cloudpartner.azure.com/)然後重新發佈供應項目。 以下是一些常見的原因，若要更新供應項目：

- 更新 Power BI 中的應用程式的內容，並從新封裝的應用程式取得安裝 URL
- 若要更新供應項目的 Azure Marketplace 中繼資料 （銷售、 行銷，或支援資訊和資產）
 
使用入口網站**比較**並**歷程記錄**諸如此類的功能來追蹤變更。

## <a name="common-update-operations"></a>一般更新作業

在 AppSource 中發行供應項目之後，就無法變更某些屬性，Power BI 應用程式供應項目。 不允許的變更，包括**優惠識別碼**並**發行者識別碼**。 但您還是可以變更各種不同的特性。 以下是一些常見的變更。

### <a name="update-app-content-in-power-bi"></a>更新 Power BI 中的應用程式內容

Power BI 應用程式通常會定期更新新的內容、 安全性修補程式、 功能等等。 變更應用程式時，請依照下列步驟更新其安裝 URL:

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2.  在 [所有供應項目] 之下，尋找您要更新的供應項目。
3.  在 **技術的資訊**索引標籤上，輸入新的安裝 URL。
4.  選取 **發佈**啟動工作流程，以將新的應用程式發佈至 AppSource。


### <a name="update-the-offers-marketplace-metadata"></a>更新供應項目的 Marketplace 中繼資料

您的供應項目 Marketplace 中繼資料包括您的公司名稱、 標誌和其他資訊。 若要更新的中繼資料，請遵循下列步驟：

1.  登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)。
2.  在 [所有供應項目] 之下，尋找您要更新的供應項目。
3.  在 [**店面詳細資料**索引標籤上，變更中繼資料文件中的指示[Power BI 應用程式店面詳細資料] 索引標籤](./cpp-storefront-details-tab.md)。
4.  選取 [發佈] 啟動工作流程，以發佈變更。


## <a name="the-compare-feature"></a>比較功能

當您更新已發佈的供應項目時，您可以檢查您所做的變更。 若要使用**比較**功能：

1.  在編輯程序中的任何時間點，選取 [供應項目的**比較**] 按鈕。

    ![比較按鈕](./media/compare-feature-button.png)

2.  並排檢視行銷資產與中繼資料。


## <a name="history-of-publishing-actions"></a>發佈動作的歷程記錄

若要檢視您的供應項目的發行記錄，在 Cloud Partner 入口網站的左側，開啟**歷程記錄** 索引標籤。這裡您會看到記錄的時間戳記的動作，您在 AppSource 上提供。

## <a name="next-steps"></a>後續步驟

在 Cloud Partner 入口網站中，定期使用[Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md)來尋找您的 Marketplace 客戶和使用方式的實用資訊。  
