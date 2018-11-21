---
title: Azure SQL 資料倉儲版本資訊 | Microsoft Docs
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/11/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: b702d375f7a66843918a960ca3783c078eac541e
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51579286"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 資料倉儲版本資訊

Azure SQL 資料倉儲是雲端式企業資料倉儲 (EDW)，利用大量平行處理 (MPP) 以快速地在數以 PB 計的資料中執行複雜的查詢。 使用 SQL 資料倉儲作為巨量資料解決方案的重要元件。 使用簡單的 PolyBase T-SQL 查詢將巨量資料匯入 SQL 資料倉儲，然後使用 MPP 功能來執行高效能分析。 整合及分析時，資料倉儲將會變成您的企業可以信賴的真正單一版本。

按一下下列連結，深入了解您可在最新版 Azure SQL 資料倉儲中取得的新功能和改進功能。 您應可在識別的維護排程期間收到這些服務更新。

- [2018 年 10 月](./release-notes-october-2018.md)
- [2018 年 9 月](./release-notes-september-2018.md)
- [2018 年 8 月](./release-notes-august-2018.md)
- [2018 年 7 月](./release-notes-july-2018.md)
- [2018 年 6 月](./release-notes-june-2018.md)
- [2018 年 5 月](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>檢查已套用到資料倉儲的程式碼版本

若要確認資料倉儲已套用哪個版本： 透過 SSMS 連線到資料倉儲，並執行下列語法來傳回目前的 SQL 資料倉儲版本。

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

範例輸出：![SQL 資料倉儲版本](./media/release-notes/sql_data_warehouse_version.png)

請使用識別的日期來確認哪個版本已套用至您的 Azure SQL 資料倉儲。 


## <a name="next-steps"></a>後續步驟
- [深入了解](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule)如何檢視維護排程。 
- [深入了解](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule)如何變更維護排程。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric)如何使用 Azure 監視器來建立、檢視和管理警示。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)適用於記錄警示規則的 Webhook 動作。
- [深入了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服務健康狀態
