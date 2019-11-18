---
title: 監視並改善效能
description: Azure SQL Database 會提供可協助您找出可改善目前查詢效能區域的效能工具。
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 11/14/2019
ms.openlocfilehash: 474c2f4f00374ce785b81fe048e11cb353b3078a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151162"
---
# <a name="monitor-and-improve-performance"></a>監視並改善效能

Azure SQL Database 可找出資料庫中的潛在問題，並透過提供智慧型微調動作和建議來建議可以改善工作負載效能的動作。

## <a name="performance-tuning-options"></a>效能微調選項

[智慧效能] 底下的 [資料庫] 導覽功能表上可用 Azure SQL Database 可用的效能微調選項：

| 效能微調選項 | 單一資料庫和集區資料庫支援 | 實例資料庫支援 |
| :----------------------------- | ----- | ----- |
| **效能總覽**-監視資料庫的所有效能活動。 | yes | 否 | 
| **效能建議**-顯示可改善工作負載效能的效能建議。 | yes | 否 | 
| **查詢效能深入解析**-顯示資料庫上最常使用查詢的效能。 | yes | 否 | 
| **自動調整**-使用 Azure SQL Database 自動優化您的資料庫效能。 | yes | 否 | 

## <a name="performance-overview"></a>效能總覽

此檢視會提供您的資料庫效能的摘要，並可協助您進行效能調整和疑難排解。 

![Azure SQL Database 的效能總覽](./media/sql-database-performance/performance-overview-annotated.png)

* [建議] 圖格會提供適用於資料庫的微調建議分解 (如果有多個建議，則會顯示前三個)。 按一下此圖格，即會帶您前往 [**效能建議[]](#performance-recommendations)** 。 
* [微調活動] 圖格會提供資料庫進行中和已完成微調動作的摘要，讓您快速檢視調整活動的歷程記錄。 按一下此圖格會帶您前往您的資料庫的完整微調記錄檢視。
* [自動調整] 圖格會顯示資料庫的[自動調整設定](sql-database-automatic-tuning-enable.md) (自動套用至資料庫的微調選項)。 按一下此圖格會開啟 [自動化組態] 對話方塊。
* [資料庫查詢] 圖格會顯示資料庫的查詢效能摘要 (整體 DTU 使用量和排名最前面的資源取用查詢)。 按一下此圖格，即會帶您前往 [**查詢效能深入解析[]](#query-performance-insight)** 。

## <a name="performance-recommendations"></a>效能建議

此頁面提供可改善資料庫效能的智慧型[微調建議](sql-database-advisor.md)。 此頁面上顯示的建議類型如下：

* 要建立或卸除哪些索引的建議。
* 在資料庫中發現結構描述問題時的建議。
* 當查詢可以受益於參數化查詢時的建議。

![Azure SQL Database 的效能建議](./media/sql-database-performance/performance-recommendations-annotated.png)

您也可以找到過去所套用微調動作的完整歷程記錄。

在[尋找和套用效能建議](sql-database-advisor-portal.md)文章中，了解如何找到和套用效能建議。

## <a name="query-performance-insight"></a>查詢效能深入解析

[查詢效能深入解析](sql-database-query-performance.md) 提供了下列各項，讓您得以花費較少的時間來針對資料庫效能進行疑難排解：

* 更深入的資料庫資源 (DTU) 取用分析。 
* 排名最前面的 CPU 取用查詢，進行微調有可能會改善效能。 
* 向下鑽研查詢詳細資料的功能。 

  ![Azure SQL Database 的查詢效能深入解析](./media/sql-database-performance/query-performance-insights-annotated.png)

如需此頁面的詳細資訊，可在 **[如何使用查詢效能深入解析](sql-database-query-performance.md)** 一文中找到。

## <a name="automatic-tuning"></a>自動微調

Azure SQL 資料庫能透過套用[效能建議](sql-database-advisor.md)來自動調整資料庫效能。 若要啟用它，請參閱[如何啟用自動調整](sql-database-automatic-tuning-enable.md)。

  ![Azure SQL Database 的自動調整](./media/sql-database-performance/automatic-tuning-annotated.png)

若要深入了解，請參閱[自動調整文章](sql-database-automatic-tuning.md)。

## <a name="additional-resources"></a>其他資源

* [單一資料庫的 Azure SQL Database 效能指引](sql-database-performance-guidance.md)
* [何時使用彈性集區？](sql-database-elastic-pool-guidance.md)
