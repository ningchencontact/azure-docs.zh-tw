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
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131360"
---
## <a name="timeout"></a>函式應用程式逾時持續期間 

FunctionTimeout 屬性中所定義的函式應用程式的逾時持續時間[host.json](../articles/azure-functions/functions-host-json.md#functiontimeout)專案檔。 下表顯示的預設值和最大值，以分鐘為單位的這兩個方案和兩個執行階段版本：

| 規劃 | 執行階段版本 | 預設值 | 最大值 |
|------|---------|---------|---------|
| 耗用量 | 1.x | 5 | 10 |
| 耗用量 | 2.x | 5 | 10 |
| App Service 方案 | 1.x | 無限 | 無限 |
| App Service 方案 | 2.x | 30 | 無限 |

> [!NOTE] 
> 函式應用程式逾時設定，無論 230 秒會是 HTTP 觸發函式可以長時間來回應要求的最大數量。 這是因為[預設的 Azure Load Balancer 的閒置逾時](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)。 對於較長的處理時間，請考慮使用[長期函式非同步模式](../articles/azure-functions/durable/durable-functions-concepts.md#async-http)或是[延後實際工作，並傳回立即回應](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)。
