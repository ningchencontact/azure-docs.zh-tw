---
title: Azure 中的記錄和計量 |Microsoft Docs
description: 概述 Azure 中的診斷記錄，提供有關 Azure 資源作業的豐富、經常性資料。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262358"
---
# <a name="logs-and-metrics-in-azure"></a>Azure 中的記錄和計量
有不同的[記錄](data-platform-logs.md)和[計量](data-platform-metrics.md)

監視 Azure 中的應用程式和服務可以分成儲存在[azure 資料平臺](data-platform.md)中的。 

記錄和計量可以分成兩個類別

- 不需要任何設定即可自動產生的平臺-記錄和計量 



| 圖層 | 平臺記錄 | 平臺計量 | 自訂記錄檔 | 自訂計量 |
|:---|:---|:---|:---|:---|
| 應用程式  | | | | |
| 客體作業系統     | 活動訊號 |  | 診斷擴充功能<br>Log Analytics 代理程式 | 診斷擴充功能 |
| Resource     | [資源記錄](resource-logs-overview.md)<br>（每項服務的特定） | [資源計量](metrics-supported.md)<br>（每項服務的特定） | | [自訂計量](metrics-custom-overview.md) |
| 訂閱 | [活動記錄檔](activity-logs-overview.md) | | | |
| 租用戶       | 

## <a name="next-steps"></a>後續步驟

* [將資源診斷記錄串流至**事件中樞**](resource-logs-stream-event-hubs.md)
* [使用 Azure 監視器 REST API 變更資源診斷設定](https://docs.microsoft.com/rest/api/monitor/)
* [使用 Azure 監視器分析來自 Azure 儲存體的記錄](collect-azure-metrics-logs.md)
