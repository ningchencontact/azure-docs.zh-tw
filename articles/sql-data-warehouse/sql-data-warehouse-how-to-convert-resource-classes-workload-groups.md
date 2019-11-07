---
title: 將資源類別轉換成工作負載群組
description: 瞭解如何建立與 Azure SQL 資料倉儲中的資源類別類似的工作負載群組。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685702"
---
# <a name="convert-resource-classes-to-workload-groups"></a>將資源類別轉換成工作負載群組
工作負載群組提供隔離和包含系統資源的機制。  此外，工作負載群組可讓您設定在其中執行之要求的執行規則。  查詢超時執行規則允許取消失控查詢而不需要使用者介入。  本文說明如何採用現有的資源類別，並使用類似的設定來建立工作負載群組。  此外，也會新增選擇性的查詢超時規則。

## <a name="understanding-the-existing-resource-class-configuration"></a>瞭解現有的資源類別設定
工作負載群組需要名為 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 的參數，指定每個要求配置的整體系統資源百分比。  藉由配置並行位置，針對[資源類別](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes)進行資源配置。  若要判斷要為 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`指定的值，請使用 sys.databases dm_workload_management_workload_groups_stats <link tbd> DMV。  例如，下列查詢查詢會傳回可用於 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 參數的值，以建立類似 staticrc40 的工作負載群組。   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 工作負載群組會根據整體系統資源的百分比來運作。  

由於工作負載群組是根據整體系統資源的百分比來運作，因此當您相應增加和減少時，配置給靜態資源類別的資源百分比會與整體系統資源變更相關。  例如，DW1000c 的 staticrc40 會配置整體系統資源的9.6%。  在 DW2000c，會配置19.2%。  如果您想要相應增加以進行並行處理，或針對每個要求配置更多資源，此模型會很類似。   

## <a name="create-workload-group"></a>建立工作負載群組
使用已知的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`，您可以使用建立工作負載群組 <link> 語法來建立工作負載群組。  您可以選擇性地指定大於零的 `MIN_PERCENTAGE_RESOURCE`，以隔離工作負載群組的資源。  此外，您可以選擇性地指定小於 100 `CAP_PERCENTAGE_RESOURCE`，以限制工作負載群組可以使用的資源量。  

下列範例會將 `MIN_PERCENTAGE_RESOURCE` 設定為專用於9.6% 的系統資源，以 `wgDataLoads` 並保證一個查詢能夠執行所有的時間。  此外，`CAP_PERCENTAGE_RESOURCE` 設定為38.4%，並將此工作負載群組限制為四個並行要求。  藉由將 `QUERY_EXECUTION_TIMEOUT_SEC` 參數設定為3600，任何執行超過1小時的查詢都會自動取消。

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>建立分類器
先前，對資源類別的查詢對應是使用[sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class)完成。  若要達到相同的功能，並將要求對應至工作負載群組，請使用[建立工作負載分類器](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)語法。  使用 sp_addrolemember 只允許您根據登入將資源對應至要求。  分類器除了登入以外，還提供其他選項，例如：
    - 標籤
    - session
    - 時間下列範例會從 `AdfLogin` 登入指派查詢，而此登入也會將[選項標籤](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label)設定為 `factloads` 到上方建立的工作負載 `wgDataLoads` 群組。

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>使用範例查詢進行測試
以下是範例查詢和 DMV 查詢，以確保工作負載群組和分類器已正確設定。

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 * 
  FROM nation 
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command 
  FROM sys.dm_pdw_exec_requests 
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>後續步驟

工作負載隔離-連結 tbd 如何建立工作負載群組-連結 tbd