---
title: Azure 應用程式供應專案設定 |Azure Marketplace
description: 設定 Azure 應用程式供應項目的供應項目設定。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: c5e00f77ffeec61fb4fee4b4e8eb72b7c7ed1077
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827533"
---
# <a name="azure-application-offer-settings-tab"></a>Azure 應用程式供應項目設定索引標籤

本文說明如何設定 Azure 應用程式的供應項目設定。

[ **Azure 應用程式 > 新增供應**專案] 頁面隨即開啟，並將焦點放在**供應專案設定**索引標籤上。功能變數名稱上附加的星號（*）表示它是必要的。

![供應項目身分識別表單](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>供應項目識別設定

在 [供應項目識別]中，您需要提供下表列出的欄位資訊。  

|    欄位         |       描述                                                            |
|  ---------       |     ---------------                                                          |
| **供應專案識別碼\***       | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品的 URL 與深入解析報告中。 識別碼長度上限為 50 個字元，可使用小寫英數字元和連字號 (-)。 （識別碼的結尾不能是破折號）。**注意：** 供應專案上線之後，即無法變更此欄位。 <br> 例如，若 Contoso 發佈了一個識別碼為 **sample-container** 的供應項目，其會收到指派的 Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`。 |
| **發行者識別碼\***     | 您的組織在 Microsoft Azure Marketplace 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 儲存供應項目之後，就無法再變更此值。 |
| **名稱\***          | 您供應項目的顯示名稱。 此名稱會顯示在 Microsoft Azure Marketplace 和 Cloud Partner 入口網站中。 它最多不能超過 50 個字元。 建議在產品中加入明顯的品牌名稱。 請勿包含您的組織名稱，除非這是您的產品行銷方式。 如果您要透過其他網站和出版物行銷此供應項目，請確保名稱在所有出版物上都相同。 |
|  |  |

選取 [儲存] 以儲存供應項目設定。

## <a name="next-steps"></a>後續步驟

使用 [SKU](./cpp-skus-tab.md) 索引標籤來設定供應項目之 SKU。