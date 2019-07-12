---
title: 適用於 MySQL 的 Azure 資料庫中的查詢效能深入解析
description: 本文說明適用於 MySQL 的 Azure 資料庫中的查詢效能深入解析功能
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8f142933ebf955cbe3aa119f42779109fb6ef7db
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67589078"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫中的查詢效能深入解析

**適用於：**  適用於 MySQL 5.7 的 Azure 資料庫

> [!NOTE]
> 查詢效能深入解析為預覽狀態。

查詢效能深入解析可協助您快速找出執行時間最長的查詢、一段時間後的變化情形，以及受到哪些等候的影響。

## <a name="common-scenarios"></a>常見案例

### <a name="long-running-queries"></a>長時間執行的查詢

- 識別在過去 X 小時中執行最久的查詢
- 識別等候資源的前 N 項查詢
 
### <a name="wait-statistics"></a>等候統計資料

- 了解查詢的等候本質。
- 資源等候和資源爭用所在的了解趨勢

## <a name="permissions"></a>Permissions

**擁有者** 或是 **參與者** 檢視中查詢效能深入解析的查詢的文字所需的權限。 **讀取器** 可以檢視圖表和資料表，但不是查詢文字。

## <a name="prerequisites"></a>先決條件

資料函式的查詢效能深入解析，必須存在於 [查詢存放區](concepts-query-store.md)。

## <a name="viewing-performance-insights"></a>檢視效能深入解析

Azure 入口網站中的[查詢效能深入解析](concepts-query-performance-insight.md)檢視會以視覺效果呈現來自查詢存放區的重要資訊。

在您的 Azure Database for MySQL 伺服器的入口網站頁面中，選取 **查詢效能深入解析** 之下 **智慧型效能** 功能表列的部分。

### <a name="long-running-queries"></a>長時間執行的查詢

 **長時間執行的查詢** 索引標籤會顯示前 5 個查詢的平均持續時間，每次執行，每隔 15 分鐘彙總。 您可以檢視更多查詢，從選取 **的查詢數目** 下拉式清單。 當您這樣做時，特定查詢識別碼的圖表色彩可能會有所變更。

您可以在圖表中按一下並拖曳來縮小到特定時間範圍。 或者，使用 in 和 out 圖示為縮放層級，分別檢視一段小或較大的時間。

![查詢效能深入解析長時間執行的查詢](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>等候統計資料

> [!NOTE]
> 等候統計資料是為了疑難排解查詢效能問題。 建議您開啟的僅供疑難排解之用。

等候統計資料會提供特定的查詢執行期間發生的等候事件的檢視。 深入了解在等候事件類型[MySQL 引擎文件](https://go.microsoft.com/fwlink/?linkid=2098206)。

選取  **Wait Statistics**索引標籤，檢視對應的視覺效果上伺服器的等候時間。

等候統計資料檢視中顯示的查詢會依出現在指定的時間間隔，最大等待的查詢。

![查詢效能深入解析會等候統計資料](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>後續步驟

- 深入了解[監視和微調](concepts-monitoring.md)適用於 MySQL 的 Azure 資料庫中。