---
title: Azure 串流分析工作狀態
description: 本文說明串流分析工作的不同狀態
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789445"
---
# <a name="azure-stream-analytics-job-states"></a>Azure 串流分析工作狀態

串流分析工作在任何指定的時間可能處於四個狀態之一。 您可以在 Azure 入口網站中串流分析工作的 [概觀] 頁面上找到工作狀態。 

| State | 描述 | 建議動作 |
| --- | --- | --- |
| **執行中** | 您的工作正在 Azure 上執行，讀取來自定義之輸入來源的事件、處理它們，並將結果寫入至設定的輸出接收器。 | 監視[關鍵計量](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)是追蹤工作效能的最佳做法。 |
| **已停止** | 您的工作已停止且不會處理事件。 | NA | 
| **已降級** | 暫時性錯誤可能正在影響您的工作。 串流分析工作會立即嘗試復原那些錯誤，並返回「正在執行」狀態 (在幾分鐘內)。 這些錯誤的發生原因可能是網路問題、其他 Azure 資源的可用性、還原序列化錯誤等等。當工作處於降級狀態時，您的工作效能可能會受到影響。| 若要深入了解這些暫時性錯誤的原因，您可以查看[診斷或活動記錄](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)。 若是在還原序列化錯誤的情況下，建議採取矯正措施來確保事件不是格式錯誤。 如果工作不斷地達到資源使用量限制，請嘗試增加 SU 數量，或[將您的工作平行化](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)。 在您無法採取任何動作的其他情況下，串流分析將會嘗試復原至 [正在執行]  狀態。  |
| **已失敗** | 您的工作發生嚴重錯誤而造成失敗狀態。 無法讀取和處理事件。 造成工作最後處於失敗狀態的常見原因是執行階段錯誤。 | 您可以[設定警示](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal)，如此一來當工作進入失敗狀態時，您就會收到通知。 <br> <br>您可以使用[活動和診斷記錄](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs)來進行偵錯，以找出根本原因並解決問題。|

## <a name="next-steps"></a>後續步驟
* [設定 Azure 串流分析工作的警示](stream-analytics-set-up-alerts.md)
* [串流分析中的可用計量](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [使用活動和診斷記錄進行疑難排解](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
