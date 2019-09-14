---
title: 包含檔案
description: 包含檔案
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: 0bd66699142e9e03f4a344d499624fe207cb9a45
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963566"
---
## <a name="timeout"></a>函數應用程式超時時間 

函式應用程式的超時期間是由[主機. json](../articles/azure-functions/functions-host-json.md#functiontimeout)專案檔中的 functionTimeout 屬性所定義。 下表顯示方案和兩個執行階段版本中的預設和最大值（以分鐘為單位）：

| 方案 | Runtime 版本 | 預設 | 最大值 |
|------|---------|---------|---------|
| 使用情況 | 1.x | 5 | 10 |
| 使用情況 | 2.x | 5 | 10 |
| App Service | 1.x | 無限制 | 無限制 |
| App Service | 2.x | 30 | 無限制 |

> [!NOTE] 
> 不論函數應用程式的超時設定為何，230秒是 HTTP 觸發函式回應要求所能採取的最大時間量。 這是因為 Azure Load Balancer 的[預設空閒超時](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 針對較長的處理時間，請考慮使用[Durable Functions 非同步模式](../articles/azure-functions/durable/durable-functions-overview.md#async-http)，或[延遲實際的工作並傳回立即回應](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
