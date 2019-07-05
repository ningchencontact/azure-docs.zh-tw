---
title: 適用於 MariaDB 的 Azure 資料庫中的效能建議
description: 本文說明適用於 MariaDB 的 Azure 資料庫中的 [效能建議] 功能
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a2f9b7597022822272692d20976e1da654b9d524
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462063"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的效能建議

**適用範圍：** 適用於 10.2 的 MariaDB 的 azure 資料庫

> [!NOTE]
> 效能建議處於預覽狀態。

[效能建議] 功能會分析您的資料庫，以建立自訂的建議，以改善效能。 若要產生的建議，分析會探討各種資料庫特性，包括結構描述。 啟用[查詢存放區](concepts-query-store.md)您充分利用 [效能建議] 功能的伺服器上。 如果效能結構描述是 OFF，開啟 [查詢存放區可讓 performance_schema 和效能] 功能所需的結構描述 instruments 的子集。 實作任何效能建議後，您應該測試來評估這些變更的影響效能。

## <a name="permissions"></a>權限

需要**擁有者**或**參與者**權限，才能使用 [效能建議] 功能執行分析。

## <a name="performance-recommendations"></a>效能建議

[效能建議](concepts-performance-recommendations.md)功能可分析整部伺服器的工作負載，來找出可能可以改善效能的索引。

開啟**效能建議**從**智慧型效能**MariaDB 伺服器在 Azure 入口網站頁面上的功能表列的部分。

![[效能建議] 登陸頁面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

選取 **分析**選擇的資料庫，就會開始分析。 根據您的工作負載，分析可能需要幾分鐘才能完成。 分析完成後，入口網站中會有通知。 分析執行深度檢查您的資料庫。 我們建議您在離峰時段執行分析。

**建議**視窗會顯示一份建議，如果找不到任何並產生這項建議的相關的查詢識別碼。 查詢識別碼，您可以使用[mysql.query_store](concepts-query-store.md#mysqlquery_store)檢視，以深入了解查詢。

![效能建議新頁面](./media/concepts-performance-recommendations/performance-recommendations-result.png)

不會自動套用建議。 若要套用建議，複製查詢文字，並從您的用戶端選擇的執行。 請務必測試和評估建議用來監視。

## <a name="recommendation-types"></a>建議類型

目前，只有*Create Index*支援建議。

### <a name="create-index-recommendations"></a>建立索引建議

*建立索引*建議建議來加快最常執行或費時的查詢工作負載中的新索引。 此建議類型需要[查詢存放區](concepts-query-store.md)啟用。 查詢存放區會收集查詢的資訊，並提供詳細的查詢執行階段和頻率統計分析用來提出建議。

## <a name="next-steps"></a>後續步驟

- 深入了解[監視和微調](concepts-monitoring.md)適用於 MariaDB 的 Azure 資料庫中。