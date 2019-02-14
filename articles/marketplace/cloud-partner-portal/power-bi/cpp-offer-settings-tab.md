---
title: Power BI 應用程式供應項目的供應項目設定 - Azure Marketplace | Microsoft Docs
description: 為 Microsoft AppSource Marketplace 的 Power BI 應用程式供應項目設定供應項目設定。
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665809"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 應用程式供應項目設定索引標籤

服務應用程式的 [新增供應項目] 頁面會在名為 [供應項目設定] 的第一個索引標籤中開啟。  您將在此索引標籤中提供您供應項目的主要識別碼和名稱。欄位名稱上附加星號 (*) 表示為必填欄位。

![供應項目設定索引標籤](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>供應項目設定欄位 

在**供應項目設定**索引標籤中，您必須提供以下必要欄位資訊。

|  欄位        |  說明                                                               |
|---------------|----------------------------------------------------------------------------|
| **供應項目識別碼**  | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品 URL、Resource Manager 範本和計費報告中。 識別碼長度上限為 50 個字元，只能包含小寫英數字元和連字號 (-)，但不能以連字號結尾。 供應項目上線之後即無法變更此欄位。 例如，若 Contoso 發佈了供應項目識別碼為 `sample-SvcApp` 的供應項目，則會收到指派的 AppSource URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`。      |
| **發行者** | 您的組織在 [AppSource](https://appsource.microsoft.com) 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 供應項目儲存後，不可再修改此欄位內容。                         |
| **名稱**      | 您的供應項目顯示名稱。 此名稱會顯示在 AppSource 和 Cloud Partner 入口網站中。 它最多不能超過 50 個字元。 建議在產品中包含明顯的品牌名稱。 請勿在此包含您的組織名稱，除非這是您應用程式的行銷方式。 如果您要透過其他網站和出版物行銷此供應項目，請確保名稱在所有出版物上都相同。    <br/>如果您在 Power BI 應用程式的預覽期間以預覽模式發行供應項目，請將字串 `(Preview)` 附加至應用程式名稱，例如 `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>後續步驟

在下一個索引標籤中，您將為供應項目指定[技術資訊](./cpp-technical-info-tab.md)。
