---
title: 供應項目設定為 Power BI 應用程式供應項目 |Azure Marketplace
description: 設定 Power BI 應用程式發行到 Microsoft AppSource marketplace 的供應項目設定。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: be2c2b4f5d9461aa0fdc6dde89931ed4b6418ced
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943453"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 應用程式供應項目設定索引標籤

當您開啟**新增供應項目**頁面上的服務應用程式，您第一次看到**供應項目設定** 索引標籤。此索引標籤上，才提供的主要識別碼，而且您的供應項目名稱。星號 （*） 表示必要的欄位。

![供應項目設定索引標籤](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>供應項目設定欄位 

在 **供應項目設定**索引標籤上，您需要下列的必要欄位中輸入資訊。 必要欄位由星號 (*) 標示。

|  欄位        |  描述                                                               |
|---------------|----------------------------------------------------------------------------|
| **供應項目識別碼\***  | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品 URL、Azure Resource Manager 範本和計費報告中。 長度上限是 50 個字元。 它只能包含小寫英數字元和連字號 （-）。 它的結尾不能以連字號。 供應項目上線之後，就無法變更此識別項。 如果 Contoso 發佈供應項目與供應項目 ID `sample-SvcApp`，供應項目指派的 AppSource URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`。      |
| **Publisher\*** | 您的組織在 [AppSource](https://appsource.microsoft.com) 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 儲存供應項目後，就無法再變更此值。                         |
| **名稱\***      | 供應項目的顯示名稱。 此名稱會出現在 AppSource 上和 Cloud Partner 入口網站。 長度上限是 50 個字元。 使用可辨識為您的產品品牌名稱。 請勿包含您的組織名稱，除非應用程式具有該名稱您的行銷。 如果您在其他網站和發行集上提供這項優惠，請在所有發行集使用相同的名稱。    <br/>如果您的 Power BI 應用程式發行供應項目在預覽期間，將字串加入`(Preview)`結尾的應用程式的名稱，像這樣： `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>後續步驟

在下一步 索引標籤中，您會指定[技術的資訊](./cpp-technical-info-tab.md)為您的供應項目。
