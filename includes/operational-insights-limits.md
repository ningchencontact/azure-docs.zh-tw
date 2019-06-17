---
title: 包含檔案
description: 包含檔案
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 06/10/2019
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: c5fedc59c80c68fc222693a67664ef60ddd210a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133782"
---
每個 Log Analytics 工作區，在目前耗用量為基礎的定價層在 2018 年 4 月引進，適用下列限制：

|     | 每 GB 2018 |
| --- | --- | 
| 每日所收集的資料量 | None |
| 資料保留期間 | 30 至 730 天<sup>1</sup> |

每個 Log Analytics 工作區定價層的最新舊版適用下列限制：

|  | 免費 | 獨立 （每 GB) | 每個節點 (OMS) |
| --- | --- | --- | --- | --- | --- |--- |
| 每日所收集的資料量 |500 MB<sup>2</sup> |None |None |
| 資料保留期間 |7 天 | 30 至 730 天<sup>1</sup> | 30 至 730 天<sup>1</sup> |

每個 Log Analytics 工作區定價層的最舊的舊版適用下列限制：

|  | 標準 | 進階 | 
| --- | --- | --- | --- | --- | --- |--- |
| 每日所收集的資料量 | None | None | 
| 資料保留期間 |30 天 | 365 天 |

<sup>1</sup>超過 31 天的資料保留期可供其他費用。 深入了解 [Azure 監視器價格](https://azure.microsoft.com/pricing/details/monitor/)。

<sup>2</sup>當您的工作區達到每日資料傳輸限制 500 MB，資料分析會停止，並在隔天開頭繼續。 一天是以 UTC 為基礎。

>[!NOTE]
>根據多久您已使用 Log Analytics，您可能必須存取舊版的定價層。 深入了解[定價層的 Log Analytics 舊版](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 
>

下列限制適用於每個訂用帳戶的 Azure Log Analytics 資源 （工作區）。

| 定價層    | 每個訂用帳戶的工作區的數目 | 註解
| --- | --- | --- |
| 免費層  | 10 | 此限制無法增加。 |
| 免費 」 以外的所有層 | N/A | 您受限於資源群組內的資源數目和每個訂用帳戶的資源群組數目。 | 

下列限制適用於 Log Analytics Api:

| Category | 限制 | 註解
| --- | --- | --- |
| 資料收集器 API | 單一貼文的大小上限為 30MB。<br>欄位值的大小上限為 32 KB。 | 將較大的磁碟區分割成多篇文章。<br>超過 32 KB 的欄位會被截斷。 |
| 搜尋 API | 針對非彙總資料，傳回 5,000 筆記錄。<br>500,000 個彙總資料的記錄。 | 彙總的資料是包含搜尋`summarize`命令。
 
