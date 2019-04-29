---
title: Power BI 應用程式供應項目的技術資訊 - Azure Marketplace | Microsoft Docs
description: 針對 Microsoft AppSource Marketplace 的 Power BI 應用程式供應項目設定技術資訊欄位。
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
ms.openlocfilehash: ca77da897eed51c8d832cad7052c2144d6ada562
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725752"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI 應用程式 [技術資訊] 索引標籤

在 [**新增供應項目**頁面上，使用**技術資訊**提供 Power BI 的安裝程式，封裝 URL 和您要驗證新的供應項目所需的其他資訊] 索引標籤。  初始版本中，所有的 Power BI 應用程式可以使用，且可供從 AppSource 下載。 基於這個原因，您無法定義此供應項目類型的庫存單位 (Sku)。

![技術的資訊 索引標籤](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>[技術資訊] 欄位 

在 **技術的資訊**索引標籤上，請完成下表中描述的欄位。 星號 （*） 的欄位標籤的結尾會表示為必要欄位。

|        欄位          |  描述                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **安裝程式 URL**     | 當您發佈應用程式，並將其升階到生產環境時，power BI 就會產生此 URL。  如需詳細資訊，請參閱 <<c0> [ 在 Power BI 中發佈的儀表板和報表的應用程式](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。  |
|  **驗證指示**  |  如果您想，新增 指示 （最多 3,000 個字元），協助 Microsoft 驗證小組設定、 連接和測試您的應用程式。 包含一般的組態設定、 帳戶、 參數或其他可以用來測試連接的資料選項的資訊。 這項資訊是只驗證小組為可見，並用它僅供驗證之用。  |
| **此應用程式是否會建立為 Power BI 內容套件？** | 目前，此欄位只能在內部使用。 保留預設值**No**。 如果您變更設定才能**是**，您可以停止發佈程序。  |  
|  |  |


## <a name="next-steps"></a>後續步驟

在 [店面詳細資料](./cpp-storefront-details-tab.md)索引標籤上，提供您的應用程式的行銷和法律資訊。

