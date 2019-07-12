---
title: 設定 Azure SQL 資料倉儲中的工作負載重要性 |Microsoft Docs
description: 了解如何設定要求層級的重要性。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e4d410f32068b4d3035dcab0c61b7b9205103690
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588688"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>設定 Azure SQL 資料倉儲中的工作負載的重要性

設定 SQL 資料倉儲中的重要性，可讓您以影響查詢的排程。 以高重要性的查詢將會執行查詢之前，以較低的重要性。 若要指派給查詢的重要性，您需要建立工作負載分類器。

## <a name="create-a-workload-classifier-with-importance"></a>建立具有重要性的工作負載分類器

通常在資料倉儲案例中您有需要快速地執行查詢的使用者。  使用者可能是您的公司需要執行報表或使用者的主管可能是分析師執行臨機操作查詢。 您建立的工作負載分類器指派至查詢的重要性。  下列範例會使用新[建立工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)語法，來建立兩個分類器。  成員名稱可以是單一使用者或群組。 個別使用者分類的優先順序高於角色分類。 若要尋找現有的資料倉儲使用者，請執行：

```sql
Select name from sys.sysusers
```

若要建立工作負載分類器，以更高重要性執行使用者：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

若要建立一位使用者執行臨機操作查詢，以較低重要性執行的工作負載分類器：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>後續步驟
- 如需有關工作負載管理的詳細資訊，請參閱[工作負載分類](sql-data-warehouse-workload-classification.md)
- 如需有關重要性的詳細資訊，請參閱[工作負載的重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [請移至 管理和監視工作負載的重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
