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
ms.openlocfilehash: 3501ff3f92ae045019df2766bbcf7fc2c3fec5b0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768908"
---
## <a name="timeout"></a>函數應用程式超時時間 

函數應用程式的超時期間是由[主機. json](../articles/azure-functions/functions-host-json.md#functiontimeout)專案檔中的 `functionTimeout` 屬性所定義。 下表顯示計畫和不同執行階段版本的預設和最大值（以分鐘為單位）：

| 方案 | 執行階段版本 | 預設 | 最大值 |
|------|---------|---------|---------|
| 消費 | 1.x | 5 | 10 |
| 消費 | 2.x | 5 | 10 |
| 消費 | 3.x | 5 | 10 |
| App Service | 1.x | 無限制 | 無限制 |
| App Service | 2.x | 30 | 無限制 |
| App Service | 3.x | 30 | 無限制 |

> [!NOTE] 
> 不論函數應用程式的超時設定為何，230秒是 HTTP 觸發函式回應要求所能採取的最大時間量。 這是因為 Azure Load Balancer 的[預設空閒超時](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 針對較長的處理時間，請考慮使用[Durable Functions 非同步模式](../articles/azure-functions/durable/durable-functions-overview.md#async-http)，或[延遲實際的工作並傳回立即回應](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
