---
title: 在 Azure Database for PostgreSQL-單一伺服器的效能建議
description: 本文說明適用於 PostgreSQL-單一伺服器的 Azure 資料庫中的 [效能建議] 功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31d8c0fdf1b4df3ee00f3652c933b4b738384bea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65068853"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Azure 資料庫中的 PostgreSQL-單一伺服器的效能建議

**適用範圍：** Azure Database for PostgreSQL-9.6] 和 [10 的單一伺服器

[效能建議] 功能會分析您的資料庫，以建立自訂的建議，以改善效能。 若要產生的建議，分析會探討各種資料庫特性，包括結構描述。 啟用[查詢存放區](concepts-query-store.md)您充分利用 [效能建議] 功能的伺服器上。 實作任何效能建議後，您應該測試來評估這些變更的影響效能。 

## <a name="permissions"></a>Permissions
需要**擁有者**或**參與者**權限，才能使用 [效能建議] 功能執行分析。

## <a name="performance-recommendations"></a>效能建議
[效能建議](concepts-performance-recommendations.md)功能可分析整部伺服器的工作負載，來找出可能可以改善效能的索引。

開啟**效能建議**從**智慧型效能**適用於 PostgreSQL 伺服器在 Azure 入口網站頁面上的功能表列的一節。

![[效能建議] 登陸頁面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

選取 **分析**選擇的資料庫，就會開始分析。 根據您的工作負載第 analysis 可能需要幾分鐘才能完成。 分析完成後，入口網站中會有通知。 分析執行深度檢查您的資料庫。 我們建議您在離峰時段執行分析。 

**建議**視窗會顯示建議的清單，如果找不到任何。

![效能建議新頁面](./media/concepts-performance-recommendations/performance-recommendations-result.png)

不會自動套用建議。 若要套用建議，複製查詢文字，並從您的用戶端選擇的執行。 請務必測試和評估建議用來監視。 

## <a name="recommendation-types"></a>建議類型

目前，支援兩種類型的建議：*建立索引*並*卸除索引*。

### <a name="create-index-recommendations"></a>建立索引建議
*建立索引*建議建議來加快最常執行或費時的查詢工作負載中的新索引。 此建議類型需要[查詢存放區](concepts-query-store.md)啟用。 查詢存放區會收集查詢的資訊，並提供詳細的查詢執行階段和頻率統計分析用來提出建議。

### <a name="drop-index-recommendations"></a>卸除索引建議
除了偵測遺漏的索引，適用於 PostgreSQL 的 Azure 資料庫會分析現有索引的效能。 如果索引很少使用或冗餘，分析器會建議卸除它。


## <a name="next-steps"></a>後續步驟
- 深入了解在適用於 PostgreSQL 的 Azure 資料庫中進行[監視和微調](concepts-monitoring.md)。

