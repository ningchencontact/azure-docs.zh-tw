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
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 9a5472a6dfc944eb793e863704897c92b1a7572e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183347"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>在 Log Analytics 中檢視及分析資料
Azure 入口網站中有兩個選項可用來分析儲存在 Log Analytics 中的資料，以及用來建立可執行隨選分析的查詢。 您使用這些入口網站建立的查詢可用於其他功能，例如警示與儀表板。

## <a name="log-analytics-page"></a>Log Analytics 頁面
從 Log Analytics 功能表中的 [記錄] 開啟 Log Analytics 頁面。 這是使用記錄檔資料集建立查詢的新體驗。 您可以在[開始使用 Azure 入口網站中的 Log Analytics 頁面](../../azure-monitor/log-query/get-started-portal.md)

Log Analytics 頁面針對[記錄搜尋 (傳統)](#log-search-classic) 體驗提供了下列改進。

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

![進階 Analytics 入口網站](media/portals/advanced-analytics-portal.png)

### <a name="resource-logs"></a>資源記錄
新的 Log Analytics 體驗整合了多種 Azure 資源，例如虛擬機器。 這表示您可以直接透過資源的監視功能表開啟 Log Analytics 頁面，而不需要切換至 Azure 監視器或 Log Analytics 並失去資源內容。 **記錄**尚未對所有的 Azure 資源啟用，但會逐漸開始出現在不同資源類型的入口網站功能表中。

當從特定資源開啟 Log Analytics 時，其範圍就會自動限定於該資源的記錄。   如果您想要撰寫包含其他記錄的查詢，則需要從 Log Analytics 或 Azure 監視器的功能表中加以開啟。

下列選項尚無法透過 Log Analytics 的 [資源] 檢視來使用：

- 儲存
- 設定警示
- 查詢總管
- 切換至不同的工作區/資源 (目前並未規劃)


### <a name="firewall-requirements"></a>防火牆需求
您的瀏覽器需要存取下列位址，才能存取 Log Analytics 頁面與「進階分析」入口網站。  如果您的瀏覽器要透過防火牆存取 Azure 入口網站，則必須啟用這些位址的存取。

| Uri | IP | 連接埠 |
|:---|:---|:---|
| portal.loganalytics.io | 動態 | 80,443 |
| api.loganalytics.io    | 動態 | 80,443 |
| docs.loganalytics.io   | 動態 | 80,443 |


## <a name="log-search-classic"></a>記錄搜尋 (傳統)
從 Log Analytics 功能表的 [記錄 (傳統)] 或從 Azure 監視器功能表中的 [Log Analytics] 開啟記錄搜尋頁面。 這是用來處理 Log Analytics 查詢的傳統頁面，缺少 [Log Analytics 頁面](#log-analytics-page)上如前所述的其他功能。



![[記錄搜尋] 頁面](media/portals/log-search-portal.png)


## <a name="next-steps"></a>後續步驟

- 逐步執行[使用記錄搜尋的教學課程](../../azure-monitor/learn/tutorial-viewdata.md)以了解如何使用查詢語言來建立查詢
- 逐步執行[使用進階分析入口網站的課程](../../azure-monitor/log-query/get-started-portal.md)，它提供與 Log Analytics 頁面相同的體驗。

