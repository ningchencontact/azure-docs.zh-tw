---
title: 包含檔案
description: 包含檔案
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238584"
---
下列限制適用於每個訂用帳戶的 Azure Log Analytics 資源。

| Resource | 預設限制 | 註解
| --- | --- | --- |
| 每一訂用帳戶的可用工作區數目 | 10 | 此限制無法增加。 |
| 每一訂用帳戶的付費工作區數目 | N/A | 您受限於資源群組內的資源數目和每個訂用帳戶的資源群組數目。 | 

>[!NOTE]
>自 2018 年 4 月 2 日起新的訂用帳戶中的新工作區會自動使用*每 GB*定價方案。 在 4 月 2 日之前建立的現有訂用帳戶或訂用帳戶已繫結至現有 Enterprise 合約註冊，您可以繼續從新的工作區的三個定價層中選擇。 
>

下列限制適用於每個 Log Analytics 工作區。

|  | 免費 | 標準 | 高級 | 獨立 | OMS | 每 GB |
| --- | --- | --- | --- | --- | --- |--- |
| 每日所收集的資料量 |500 MB<sup>1</sup> |None |None | None | None | None
| 資料保留期間 |7 天 |1 個月 |12 個月 | 1 個月 <sup>2</sup> | 1 個月 <sup>2</sup>| 1 個月 <sup>2</sup>|

<sup>1</sup>客戶時達到其每日資料傳輸限制 500 MB，資料分析會停止，並在隔天開頭繼續。 一天是以 UTC 為基礎。

<sup>2</sup>獨立、 OMS 及每 GB 定價方案的資料保留期限可增加到 730 天。

| 類別 | 限制 | 註解
| --- | --- | --- |
| 資料收集器 API | 單一貼文的大小上限為 30MB。<br>欄位值的大小上限為 32 KB。 | 將較大的磁碟區分割成多篇文章。<br>超過 32 KB 的欄位會被截斷。 |
| 搜尋 API | 針對非彙總資料，傳回 5,000 筆記錄。<br>500,000 個彙總資料的記錄。 | 彙總的資料是包含搜尋`summarize`命令。
 
