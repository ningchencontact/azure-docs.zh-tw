---
title: 在 Azure Log Analytics 中檢視及分析資料 | Microsoft Docs
description: 本文會說明可以在 Azure Log Analytics 中用來建立和編輯記錄搜尋的入口網站。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 386aad94461fa3f2ceafb7564342797eefa2f086
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246507"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>在 Log Analytics 中檢視及分析資料
Azure 入口網站中有兩個選項可用來分析儲存在 Log Analytics 中的資料，以及用來建立可執行隨選分析的查詢。 您使用這些入口網站建立的查詢可用於其他功能，例如警示與儀表板。

## <a name="log-analytics-page-preview"></a>Log Analytics 頁面 (預覽)
從 Log Analytics 功能表中的 [記錄檔 (預覽)] 開啟 Log Analytics 頁面。 這是使用記錄檔資料集建立查詢的新體驗。 您可以在[開始使用 Azure 入口網站中的 Log Analytics 頁面](query-language/get-started-analytics-portal.md)

Log Analytics 頁面提供下列[Log 記錄檔搜尋](#log-search)體驗改進。

* 多索引標籤 – 建立不同的索引標籤以使用多個查詢。
* 豐富的視覺效果 – 各種圖表選項。
* 改進的 Intellisense 與語言自動完成。
* 語法反白顯示 – 改進查詢的可讀性。 
* 查詢總管 – 存取已儲存的查詢與函式。
* 結構描述檢視 – 檢閱您資料的結構以協助撰寫查詢。
* 共用 – 建立查詢的連結，或將查詢釘選到任何共用 Azure 儀表板。
* 智慧分析 - 找出圖表中的峰值並快速分析原因。
* 資料行選取 – 將查詢結果中的資料行排序及分組。

> [!NOTE]
> Log Analytics 頁面具有與「進階分析」入口網站相同的功能，「進階分析」入口網站是 Azure 入口網站外部的工具。 「進階分析」入口網站仍可供使用，但 Azure 入口網站中有關該入口網站的連結與其他資源將會被這個新頁面取代。

![進階分析入口網站](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


### <a name="firewall-requirements"></a>防火牆需求
您的瀏覽器需要存取下列位址，才能存取 Log Analytics 頁面與「進階分析」入口網站。  如果您的瀏覽器要透過防火牆存取 Azure 入口網站，則必須啟用這些位址的存取。

| Uri | IP | 連接埠 |
|:---|:---|:---|
| portal.loganalytics.io | 動態 | 80,443 |
| api.loganalytics.io    | 動態 | 80,443 |
| docs.loganalytics.io   | 動態 | 80,443 |


## <a name="log-search"></a>記錄搜尋
從 Log Analytics 功能表的 [記錄檔] 或從 Azure 監視器功能表中的 [Log Analytics] 開啟記錄搜尋頁面。 這適用於使用基本查詢來分析記錄資料。 它提供在不需要精通查詢語言的情況下編輯查詢的多種功能。  您可以在[使用記錄搜尋在 Azure Log Analytics 中建立記錄搜尋](log-analytics-log-search-log-search-portal.md)中取得這些功能的摘要。 


![[記錄搜尋] 頁面](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>後續步驟

- 逐步執行[使用記錄搜尋的教學課程](log-analytics-tutorial-viewdata.md)以了解如何使用查詢語言來建立查詢
- 逐步執行[使用進階分析入口網站的課程](query-language/get-started-analytics-portal.md)，它提供與 Log Analytics 頁面相同的體驗。

