---
title: SQL 資料倉儲分類 |Microsoft Docs
description: 使用分類來管理並行，重要性，以及適用於 Azure SQL 資料倉儲中查詢計算資源的指引。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: bcc09095955a28bde3ed999f23180e08485543fc
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2019
ms.locfileid: "57994010"
---
# <a name="sql-data-warehouse-workload-classification-preview"></a>SQL 資料倉儲工作負載分類 （預覽）

這篇文章說明 SQL 資料倉儲工作負載分類程序，連入要求中指定的資源類別和重要性。

> [!Note]
> 使用 SQL 資料倉儲 Gen2 上工作負載分類。

## <a name="classification"></a>分類

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

工作負載管理分類可讓工作負載原則套用至透過指派的要求[資源類別](resource-classes-for-workload-management.md#what-are-resource-classes)並[重要性](sql-data-warehouse-workload-importance.md)。

雖然有許多方法可分類資料倉儲工作負載，載入和查詢，也會是最簡單且最常見的分類。 您載入資料的 insert、 update 和 delete 陳述式。  您查詢使用選取的資料。 資料倉儲解決方案通常必須載入活動，例如指派較高的資源類別，使用更多資源的工作負載原則。 不同的工作負載原則可以套用至查詢，例如較低的重要性，相較於載入活動。

您也可以 subclassify 您載入和查詢工作負載。 Subclassification 可讓您進一步控制您的工作負載。 例如，查詢工作負載可以包含 cube 的重新整理、 儀表板查詢或臨機操作查詢。 您可以將每個具有不同的資源類別或重要性設定這些查詢工作負載。 負載也可以從 subclassification 獲益。 大型的轉換可以指派給較大資源類別。 較高的重要性可用來確保索引鍵的銷售資料之前的天氣資料的載入器或社交資料摘要。

## <a name="classification-process"></a>分類程序

SQL 資料倉儲中的分類之後，即可立即將使用者指派給具有對應的資源類別指派給它使用的角色[sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)。 描述要求以外的登入的資源類別的功能限制，且這項功能。 更豐富的方法進行分類現已與[建立工作負載分類](/sql/t-sql/statements/create-workload-classifier-transact-sql)語法。  使用此語法，SQL 資料倉儲使用者可指派重要性與資源類別的要求。  

> [!NOTE]
> 分類會評估個別要求基礎上。 以不同的方式可以分類在單一工作階段中的多個要求。

## <a name="classification-precedence"></a>分類優先順序

分類程序的一部分，優先順序是以判斷哪一個資源類別指派。 資料庫使用者為基礎的分類會將優先順序高於角色的成員資格。 如果您建立的分類器，將 UserA 資料庫使用者對應至 mediumrc 資源類別。 然後，對應至 largerc 資源類別的 RoleA 資料庫角色 （使用者 a 為成員的）。 將資料庫使用者對應至 mediumrc 資源類別的分類器會將優先順序高於 RoleA 資料庫角色會對應至 largerc 資源類別的分類器。

如果使用者是使用不同的資源類別指派或在多個分類器中相符的多個角色的成員，則使用者可以最高的資源類別指派。  此行為是與現有的資源類別指派行為一致。

## <a name="system-classifiers"></a>系統分類器

工作負載分類有系統工作負載分類器。 系統分類器會以正常的重要性，將現有的資源類別角色成員資格對應至資源類別的資源配置。 無法卸除系統分類器。 若要檢視系統分類器，您可以執行下列查詢：

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>混合使用資源類別指派的分類器

代替您建立的系統分類器會提供移轉到工作負載分類的簡單路徑。 使用分類優先順序中的資源類別角色對應，可能會導致誤判當您開始使用重要性來建立新的分類器時。

請考慮下列狀況：

•An 現有的資料倉儲具有 DBAUser 指派為 largerc 資源類別角色的資料庫使用者。 資源類別指派是使用 sp_addrolemember 來完成。
工作負載管理現在已更新 •The 資料倉儲。
‧ 若要測試新的分類語法 DBARole （即 DBAUser 的成員），已建立，並將它們對應 mediumrc 和高重要性的分類器的資料庫角色。
•When DBAUser 登入，並執行查詢，查詢將會指派給 largerc。 因為使用者的優先順序高於角色的成員資格。

若要簡化疑難排解分類誤判，我們建議您移除資源類別角色對應，當您建立工作負載分類器。  下列程式碼會傳回現有的資源類別角色成員資格。  執行[sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql)針對每個成員名稱傳回對應的資源類別。

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>後續步驟

如需有關 SQL 資料倉儲工作負載分類和重要性的詳細資訊，請參閱 <<c0> [ 建立工作負載分類](quickstart-create-a-workload-classifier-tsql.md)並[SQL 資料倉儲重要性](sql-data-warehouse-workload-importance.md)。 請參閱[sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)若要檢視查詢和指派的重要性。
