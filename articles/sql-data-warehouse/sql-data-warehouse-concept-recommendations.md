---
title: SQL 資料倉儲建議 - 概念 | Microsoft Docs
description: 深入了解 SQL 資料倉儲的建議與其產生方式
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b275f23209979e1a8068ecd99465f7b52392bc6c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082903"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL 資料倉儲建議

本文說明由 SQL 資料倉儲透過 Azure Advisor 所提供的建議。  

SQL 資料倉儲提供的建議可確保為了效能持續最佳化資料倉儲。 資料倉儲建議與 [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) 緊密整合，直接在 [Azure 入口網站](https://aka.ms/Azureadvisor)內為您提供最佳做法。 SQL 資料倉儲會每日針對作用中的工作負載，分析資料倉儲目前的狀態、收集遙測資料並顯示建議。 下面說明支援的資料倉儲建議案例以及如何套用建議的動作。

如果您有關於 SQL 資料倉儲 Advisor 的任何意見反應，或遇到任何問題，請連絡 [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com)。   

按一下[這裡](https://aka.ms/Azureadvisor)，以立即查看建議！ 此功能目前僅適用於 Gen2 資料倉儲。 

## <a name="data-skew"></a>資料扭曲

當執行工作負載時，資料扭曲可能會造成額外的資料移動或資源瓶頸。 下列文件說明如何識別資料扭曲，並透過選取最佳的散發索引鍵來防止發生資料扭曲。

- [識別並移除扭曲](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>沒有統計資料或已過期

若只有次佳的統計資料，會造成 SQL 資料倉儲查詢最佳化工具產生次佳的查詢計劃，而嚴重影響查詢效能。 下列文件說明建立及更新統計資料的最佳做法：

- [建立及更新資料表統計資料](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

若要查看受這些建議影響的資料表清單，請執行下列 [T-SQL 指令碼](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)。 Advisor 會持續執行相同的 T-SQL 指令碼，以產生這些建議。

## <a name="replicate-tables"></a>複寫資料表

針對複寫的資料表建議，Advisor 會根據下列實體特性偵測資料表候選項目：

- 複寫的資料表大小
- 資料行數目
- 資料表散發類型
- 資料分割數目

Advisor 會持續運用工作負載型啟發學習法 (例如資料表存取頻率、平均傳回的資料列數，以及與資料倉儲大小和活動相關的閾值) 來確保產生高品質的建議。 

以下說明針對每個複寫的資料表建議，您在 Azure 入口網站中可能找到的工作負載型啟發學習法：

- 掃描平均 - 過去七天內，針對每個資料表存取，從資料表傳回的資料列平均百分比
- 經常讀取、無更新 - 指出資料表在過去七天有顯示存取活動但沒有更新
- 讀取/更新比例 - 過去七天內，資料表的存取頻率相對於其更新時間的比例
- 活動 - 根據存取活動評估使用狀況。 這會將資料表存取活動與過去七天內整個資料倉儲的平均資料表存取活動做比較。 

目前 Advisor 一次最多只會顯示四個複寫的資料表候選項目，並以叢集資料行存放區索引排列最高活動優先順序。

> [!IMPORTANT]
> 複寫的資料表建議並非無懈可擊的，而且未將資料移動作業納入考量。 我們正著手將其新增為啟發學習法，但在此同時，您應該在套用建議之後，一律驗證您的工作負載。 如果您發現造成您工作負載效能變差的複寫資料表建議，請與 sqldwadvisor@service.microsoft.com 連絡。 若要深入了解複寫的資料表，請瀏覽下列[文件](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)。
