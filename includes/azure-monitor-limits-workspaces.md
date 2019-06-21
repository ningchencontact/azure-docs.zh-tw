---
title: 包含檔案
description: 包含檔案
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 21e2d3f75028d239175effa7a3608cc18ccfc95c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305260"
---
**資料收集量與保留期** 

| 層 | 每日限制 | 資料保留 | 註解 |
|:---|:---|:---|:---|
| 目前的每 GB 定價層<br>（引進 2018 年 4 月） | 沒有限制 | 30-730 天 | 超過 31 天的資料保留期可供其他費用。 深入了解 Azure 監視器定價。 |
| 舊版免費層<br>（引進 2016 年 4 月） | 500 MB | 7 天 | 當您的工作區達到每日限制 500 MB 時，資料擷取會停止，並在隔天開頭繼續。 一天是以 UTC 為基礎。 請注意，Azure 資訊安全中心所收集的資料不包含在每一天的限制此 500 MB，而且將會繼續收集超過此限制。  |
| 舊版的 「 獨立每 GB 」 層<br>（引進 2016 年 4 月） | 沒有限制 | 30 至 730 天 | 超過 31 天的資料保留期可供其他費用。 深入了解 Azure 監視器定價。 |
| 每個節點 (OMS) 的舊版<br>（引進 2016 年 4 月） | 沒有限制 | 30 至 730 天 | 超過 31 天的資料保留期可供其他費用。 深入了解 Azure 監視器定價。 |
| 舊版的標準層 | 沒有限制 | 30 天  | 無法調整保留期 |
| 舊版的進階層 | 沒有限制 | 365 天  | 無法調整保留期 |

**每個訂用帳戶的工作區的數目。**

| 定價層    | 工作區限制 | 註解
|:---|:---|:---|
| 免費層  | 10 | 此限制無法增加。 |
| 所有其他層 | 沒有限制 | 您受限於資源群組內的資源數目和每個訂用帳戶的資源群組數目。 |

**Azure 入口網站**

| Category | 限制 | 註解 |
|:---|:---|:---|
| 最大記錄檔查詢所傳回的記錄 | 10,000 | 減少查詢中使用查詢範圍、 時間範圍和篩選的結果。 |


**資料收集器 API**

| Category | 限制 | 註解 |
|:---|:---|:---|
| 單一貼文的大小上限 | 30 MB | 將較大的磁碟區分割成多篇文章。 |
| 欄位值的大小上限  | 32 KB | 超過 32 KB 的欄位會被截斷。 |

**搜尋 API**

| Category | 限制 | 註解 |
|:---|:---|:---|
| 傳回非彙總資料的最大記錄 | 5,000 | |
| 彙總資料的最大資料錄 | 500,000 | 彙總的資料是包含搜尋`summarize`命令。 |
| 傳回的資料大小上限 | 64,000,000 個位元組 (~ 61 MiB)| |
| 最大的查詢執行時間 | 10 分鐘 | 請參閱[逾時](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)如需詳細資訊。  |
| 要求率上限 | 每 30 秒，每個 AAD 使用者或用戶端 IP 位址的 200 個要求 | 請參閱[速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)如需詳細資訊。 |

**一般的工作區限制**

| Category | 限制 | 註解 |
|:---|:---|:---|
| 在資料表中的最大資料行         | 500 | |
| 資料行名稱的字元數上限 | 500 | |
| 在容量的區域 | 美國中西部 | 您目前無法建立這個區域中新的工作區，因為它是暫存的容量限制。 這項限制已規劃於 2019 年 9 月日結束處理。 |
| 匯出資料 | 目前無法使用 | 彙總，並將資料匯出至使用 Azure 函式或邏輯應用程式。 | 

>[!NOTE]
>根據多久您已使用 Log Analytics，您可能必須存取舊版的定價層。 深入了解[定價層的 Log Analytics 舊版](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 