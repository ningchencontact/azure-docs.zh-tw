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
ms.openlocfilehash: d96ef2fd318d6164e1b7dfc5c4b72d6957af0f3e
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744429"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI 應用程式 [技術資訊] 索引標籤

[新增供應項目] 頁面的 [技術資訊] 索引標籤是您提供 Power BI 安裝程式套件 URL，以及新供應項目驗證所需之任何其他資訊的位置。  針對初始版本，所有 Power BI 應用程式都是免費的，可從 AppSource 下載而不需要額外收費。 因此，您將無法為此供應項目類型定義任何庫存單位 (SKU)。

![[技術資訊] 索引標籤](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>[技術資訊] 欄位 

在 [技術資訊] 欄位中，您必須提供下列欄位。  欄位名稱上附加星號 (*) 表示為必填欄位。

|        欄位          |  說明                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **安裝程式 URL**     | 當您發佈應用程式並將其升階至生產環境時，Power BI 所產生的位址。  如需如何產生 URL 的詳細資訊，請參閱[在 Power BI 中發佈服務應用程式](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。  |
|  **驗證指示**  |  選擇性的文字指示 (上限 3000 個字元)，可協助 Microsoft 驗證小組設定、連線和測試您的應用程式，包括：一般組態設定、可用來測試 [連線資料] 選項的測試帳戶或參數等等。此資訊只有驗證小組可以查看，且僅用於驗證目的。  |
| **此應用程式是否會建立為 Power BI 內容套件？** | 目前這是內部使用的欄位。 請保留其預設值 `No`；否則變更此欄位為 `Yes` 可能會有礙發佈。  |  
|  |  |


## <a name="next-steps"></a>後續步驟

在下一個[電子店面詳細資料](./cpp-storefront-details-tab.md)索引標籤中，您將提供應用程式的行銷和法律資訊。

