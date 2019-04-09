---
title: 在 Azure 監視器中檢視及分析記錄資料 | Microsoft Docs
description: 本文描述如何使用 Azure 入口網站中的 Log Analytics，來在 Azure 監視器中建立及編輯記錄查詢。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: bwren
ms.openlocfilehash: 0e5b9b43e528b37fd994f9131f145abadb33c53b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259026"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>在 Azure 監視器中檢視及分析記錄資料
Log Analytics 是使用記錄資料及在 Azure 監視器中建立查詢的主要體驗。 從 [Azure 監視器] 功能表中的 [記錄] 開啟 Log Analytics。 您可以在[開始使用 Azure 入口網站中的 Log Analytics](get-started-portal.md) 中，取得此入口網站的資訊和檢查其功能。

Log Analytics 提供下列功能來使用記錄查詢。

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
> Log Analytics 具有與「進階分析」入口網站相同的功能，「進階分析」入口網站是 Azure 入口網站外部的工具。 「進階分析」入口網站仍可供使用，但 Azure 入口網站中有關該入口網站的連結與其他資源將會被這個新頁面取代。

![Log Analytics](media/portals/log-analytics.png)

## <a name="resource-logs"></a>資源記錄
Log Analytics 整合各種 Azure 資源，如虛擬機器。 這表示您可以直接透過資源的監視功能表開啟 Log Analytics，而不需要切換至 Azure 監視器並失去資源內容。 **記錄**尚未對所有的 Azure 資源啟用，但會逐漸開始出現在不同資源類型的入口網站功能表中。

當從特定資源開啟 Log Analytics 時，其範圍就會自動限定於該資源的記錄。   如果您想要撰寫包含其他記錄的查詢，則需要從 Azure 監視器的功能表中加以開啟。

下列選項尚無法透過 Log Analytics 的 [資源] 檢視來使用：

- 儲存
- 設定警示
- 查詢總管
- 切換至不同的工作區/資源 (目前並未規劃)


## <a name="firewall-requirements"></a>防火牆需求
您的瀏覽器需要存取下列位址，才能存取 Log Analytics。  如果您的瀏覽器要透過防火牆存取 Azure 入口網站，則必須啟用這些位址的存取。

| Uri | IP | 連接埠 |
|:---|:---|:---|
| portal.loganalytics.io | 動態 | 80,443 |
| api.loganalytics.io    | 動態 | 80,443 |
| docs.loganalytics.io   | 動態 | 80,443 |


## <a name="next-steps"></a>後續步驟

- 進行[使用 Log Analytics 教學課程](../../azure-monitor/log-query/get-started-portal.md)。
- 進行使用[記錄搜尋教學課程](../../azure-monitor/learn/tutorial-viewdata.md)。

