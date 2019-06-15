---
title: 在 Azure SQL 資料倉儲中暫停、繼續、使用 REST 調整 | Microsoft Docs
description: 透過 REST API 管理 SQL 資料倉儲中的計算能力。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/29/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 5b8652a0b08b426e708a909ff988e51eee9c0821
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476068"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>適用於 Azure SQL 資料倉儲的 REST API
在 Azure SQL 資料倉儲中用於管理計算能力的 REST API。

## <a name="scale-compute"></a>調整計算
若要變更資料倉儲單位，請使用[建立或更新資料庫](/rest/api/sql/databases/createorupdate) REST API。 下例會將裝載於 MyServer 伺服器之資料庫 MySQLDW 的資料倉儲單位設定為 DW1000。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>暫停計算

若要暫停資料庫，請使用 [暫停資料庫](/rest/api/sql/databases/pause) REST API。 下例會暫停裝載在 Server01 伺服器上的 Database02 資料庫。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>繼續計算

若要啟動資料庫，請使用 [繼續資料庫](/rest/api/sql/databases/resume) REST API。 下例會啟動裝載在 Server01 伺服器上的 Database02 資料庫。 此伺服器位於 ResourceGroup1 這個 Azure 資源群組。 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>檢查資料庫狀態

> [!NOTE]
> 目前，請檢查資料庫狀態可能會傳回 ONLINE，而資料庫完成線上的工作流程，導致連線錯誤。 您可能需要在應用程式程式碼中加入 2 到 3 分鐘的延遲，如果您使用此 API 呼叫來觸發程序連線嘗試。

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```

## <a name="get-maintenance-schedule"></a>取得維護排程
檢查已設定資料倉儲的維護排程。 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

```

## <a name="set-maintenance-schedule"></a>設定維護排程
若要設定，並更新現有的資料倉儲上 maintnenance 排程。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/maintenanceWindows/current?maintenanceWindowName=current&api-version=2017-10-01-preview HTTP/1.1

{
    "properties": {
        "timeRanges": [
                {
                                "dayOfWeek": Saturday,
                                "startTime": 00:00,
                                "duration": 08:00,
                },
                {
                                "dayOfWeek": Wednesday
                                "startTime": 00:00,
                                "duration": 08:00,
                }
                ]
    }
}

```


## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱[管理計算能力](sql-data-warehouse-manage-compute-overview.md)。

