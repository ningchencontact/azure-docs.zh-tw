---
title: Azure Cosmos DB 觸發程序記錄檔
description: 了解如何將 Azure Cosmos DB 觸發程序記錄檔公開至您的 Azure Functions 記錄管線
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: maquaran
ms.openlocfilehash: bf5216dc3b296c98176387c6e2cfff7c31daedab
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241032"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>如何設定及讀取 Azure Cosmos DB 觸發程序記錄檔

此文章說明如何設定 Azure Functions 環境，以將 Azure Cosmos DB 觸發程序記錄檔傳送至您設定的[監視解決方案](../azure-functions/functions-monitoring.md)。

## <a name="included-logs"></a>包含的記錄檔

Azure Cosmos DB 觸發程序會在內部使用[變更摘要處理器程式庫](./change-feed-processor.md)，該程式庫會產生一組可用來監視[疑難排解用途](./troubleshoot-changefeed-functions.md)內部作業的健康情況記錄檔。

健康情況記錄檔會描述在進行負載平衡情況下或在初始化期間，Azure Cosmos DB 觸發程序嘗試作業時的運作狀況。

## <a name="enabling-logging"></a>啟用記錄

若要啟用 Azure Cosmos DB 觸發程序記錄，請找出您 Azure Functions 專案或 Azure 函式應用程式中的 `host.json` 檔案，然後[設定所需的記錄層級](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson)。 您必須啟用 `Host.Triggers.CosmosDB` 的追蹤功能，如下列範例所示：

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

在 Azure Function 中部署更新的設定之後，您會看到追蹤功能中包含 Azure Cosmos DB 觸發程序記錄檔。 您可以在「類別」  `Host.Triggers.CosmosDB` 底下，自己設定的記錄提供者中檢視記錄。

## <a name="query-the-logs"></a>查詢記錄檔

在 [Azure Application Insights 的 Analytics](../azure-monitor/app/analytics.md) 中執行下列查詢，以查詢 Azure Cosmos DB 觸發程序產生的記錄檔：

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>後續步驟

* 在您的 Azure Functions 應用程式中[啟用監視](../azure-functions/functions-monitoring.md)。
* 了解在 Azure Functions 中使用 Azure Cosmos DB 觸發程序時，如何[診斷常見問題並進行疑難排解](./troubleshoot-changefeed-functions.md)。