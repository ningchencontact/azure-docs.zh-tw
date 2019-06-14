---
title: Azure 應用程式供應項目設定 |Azure Marketplace
description: 設定 Azure 應用程式供應項目的供應項目設定。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 789b783629b3cc3528eba1883b21051604cf6e14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942937"
---
# <a name="azure-application-offer-settings-tab"></a>Azure 應用程式供應項目設定索引標籤

本文說明如何設定 Azure 應用程式的供應項目設定。

[Azure 應用程式] > [新增供應項目]  頁面隨即開啟，並將焦點放在 [供應項目設定]  索引標籤上。欄位名稱上附加的星號 (*) 表示此為必填欄位。

![供應項目身分識別表單](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>供應項目識別設定

在 [供應項目識別]  中，您需要提供下表列出的欄位資訊。  

|    欄位         |       描述                                                            |
|  ---------       |     ---------------                                                          |
| **供應項目識別碼\***       | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品的 URL 與深入解析報告中。 識別碼長度上限為 50 個字元，可使用小寫英數字元和連字號 (-)。 (識別碼結尾不能使用連字號。)**附註：** 供應項目上線之後即無法變更此欄位。 <br> 例如，若 Contoso 發佈了一個識別碼為 **sample-container** 的供應項目，其會收到指派的 Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`。 |
| **發行者識別碼\***     | 您的組織在 Microsoft Azure Marketplace 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 儲存供應項目之後，就無法再變更此值。 |
| **名稱\***          | 您供應項目的顯示名稱。 此名稱會顯示在 Microsoft Azure Marketplace 和 Cloud Partner 入口網站中。 它最多不能超過 50 個字元。 建議在產品中加入明顯的品牌名稱。 請勿加入您的組織名稱，除非這是該產品的行銷方式。 如果您要透過其他網站和出版物行銷此供應項目，請確保名稱在所有出版物上都相同。 |
|  |  |

選取 [儲存]  以儲存供應項目設定。

## <a name="next-steps"></a>後續步驟

使用 [SKU](./cpp-skus-tab.md) 索引標籤來設定供應項目之 SKU。