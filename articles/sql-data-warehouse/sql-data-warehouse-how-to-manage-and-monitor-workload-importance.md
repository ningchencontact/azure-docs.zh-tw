---
title: 管理和監視 Azure SQL 資料倉儲中的工作負載重要性 |Microsoft Docs
description: 了解如何管理和監視要求層級的重要性。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: a39d71e0f8b8072cab6a3af9a2f0913973f303ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235931"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>管理和監視 Azure SQL 資料倉儲中的工作負載重要性

管理和監視 Azure SQL 資料倉儲使用 Dmv 和目錄檢視中的要求層級重要性。

## <a name="monitor-importance"></a>監視重要性

監視使用中的新 「 重要性 」 資料行的重要性[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)動態管理檢視。
下列監視顯示提交時間和開始時間查詢的查詢。 檢閱提交時間，並開始的時間，以及查看重要性如何影響排程的重要性。

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

若要查看進一步的查詢如何在排程，使用目錄檢視。

## <a name="manage-importance-with-catalog-views"></a>管理與目錄檢視的重要性

Sys.workload_management_workload_classifiers 目錄檢視包含在您的 Azure SQL 資料倉儲執行個體中的分類器的詳細資訊。 若要排除系統定義的分類器對應至資源類別會執行下列程式碼：

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

目錄檢視[sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)，包含用於建立的分類器參數的相關資訊。  下列查詢會顯示在建立 ExecReportsClassifier```membername```使用 ExecutiveReports 為值的參數：

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![查詢結果](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

若要簡化疑難排解分類誤判，我們建議您移除資源類別角色對應，當您建立工作負載分類器。 下列程式碼會傳回現有的資源類別角色成員資格。 針對每個執行 sp_droprolemember```membername```傳回對應的資源類別。
以下是檢查存在，再卸除工作負載分類器的範例：

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>後續步驟
- 如需有關分類的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)。
- 如需有關重要性的詳細資訊，請參閱[工作負載的重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [移至 設定工作負載的重要性 ](sql-data-warehouse-how-to-configure-workload-importance.md)
