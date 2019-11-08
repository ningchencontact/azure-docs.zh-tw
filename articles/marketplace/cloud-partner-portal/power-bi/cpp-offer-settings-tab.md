---
title: Power BI 應用程式供應專案的供應專案設定 |Azure Marketplace
description: 設定 Microsoft AppSource marketplace Power BI 應用程式供應專案的供應專案設定。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: ca326b10a0707d5e4b1a5f05dccc303c9ec28269
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822671"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 應用程式供應項目設定索引標籤

當您開啟服務應用程式的 [**新增供應**專案] 頁面時，您會先看到 [**供應專案設定**] 索引標籤。您會在此索引標籤上提供您供應專案的主要識別碼和名稱。星號（*）表示必要的欄位。

![供應項目設定索引標籤](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>供應項目設定欄位 

在 [**供應專案設定**] 索引標籤上，您必須在下列必要欄位中輸入資訊。 必要欄位由星號 (*) 標示。

|  欄位        |  說明                                                               |
|---------------|----------------------------------------------------------------------------|
| **供應專案識別碼\***  | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品 URL、Azure Resource Manager 範本和計費報告中。 長度上限是 50 個字元。 它只能包含小寫英數位元和連字號（-）。 結尾不能是虛線。 供應專案上線之後，即無法變更此識別碼。 如果 Contoso 發佈供應專案識別碼為 `sample-SvcApp`的供應專案，則會將 AppSource URL 指派給該供應專案 `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`。      |
| **發行者\*** | 您的組織在 [AppSource](https://appsource.microsoft.com) 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 儲存供應項目後，就無法再變更此值。                         |
| **名稱\***      | 供應專案的顯示名稱。 這個名稱會出現在 AppSource 和 Cloud Partner 入口網站上。 長度上限是 50 個字元。 使用可供您的產品辨識的品牌名稱。 除非應用程式是以該名稱行銷，否則請勿在此包含您的組織名稱。 如果您要在其他網站和發行集上提供此供應專案，請在所有發行集中使用相同的名稱。    <br/>如果您在 Power BI 應用程式的預覽期間發行供應專案，請在應用程式名稱的結尾加入字串 `(Preview)`，如下所示： `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>後續步驟

在下一個索引標籤上，您將指定供應專案的[技術資訊](./cpp-technical-info-tab.md)。
