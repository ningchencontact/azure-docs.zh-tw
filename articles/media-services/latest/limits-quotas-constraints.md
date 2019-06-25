---
title: Azure 媒體服務 v3 配額和限制 | Microsoft Docs
description: 本主題描述 Azure 媒體服務 v3 的配額和限制
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/16/2019
ms.author: juliako
ms.openlocfilehash: 709ed293dbb0550dc1bb43bf1e1e1cc50906cc31
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293434"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure 媒體服務 v3 配額和限制

本文描述 Azure 媒體服務 v3 的配額和限制。

| 資源 | 預設限制 | 
| --- | --- | 
| 每個 Azure 媒體服務帳戶的資產 | 1,000,000|
| 動態資訊清單篩選條件|100|
| 每個工作的工作輸入 | 50 (固定)|
| 每個工作的工作輸出 | 20 (固定) |
| 「 轉換 」 中的 TransformOutputs | 20 (固定) |
| 每個 JobInput 的檔案|10 (固定)|
| 檔案大小| 在某些情況下，對於媒體服務中支援處理的檔案大小上限有所限制。 <sup>(1)</sup> |
| 每個媒體服務帳戶的工作 | 500,000 <sup>(2)</sup> (固定)|
| 列出轉換|將回應分頁，每頁 1000 個轉換|
| 列出作業|將回應分頁，每頁 500 個作業|
| 每個媒體服務帳戶的即時事件 |5|
| 單一訂用帳戶的媒體服務帳戶 | 25 (固定) |
| 每個即時事件的即時輸出 |3 <sup>(3)</sup> |
| 最大即時輸出持續時間 | 25 個小時 |
| 儲存體帳戶 | 100<sup>(4)</sup> (固定) |
| 每個媒體服務帳戶的串流端點 (已停止或執行中)|2 （固定）|
| 串流原則 | 100 <sup>(5)</sup> |
| 每個媒體服務帳戶的轉換 | 100 (固定)|
| 一次與資產相關聯的唯一串流定位器 | 100<sup>(6)</sup> （固定） |
| 每個內容金鑰的原則選項 |30 | 
| 每個月的每個媒體服務 DRM 類型的授權金鑰傳遞服務，每個帳戶|1,000,000|

<sup>1</sup> 單一 blob 支援的大小上限目前在 Azure Blob 儲存體是最多 5 TB。 其他的限制適用於服務所使用的 VM 大小所根據的媒體服務中。 大小限制適用於您上傳的檔案，也因為媒體服務處理 （編碼或分析） 而產生的檔案。 如果原始程式檔超過 260 GB，您的工作可能會失敗。 

下表會顯示上媒體保留單位 S1、 S2 和 S3 的限制。 如果您的來源檔案大於資料表中定義的限制，編碼作業將會失敗。 如果編碼的持續時間較長的 4k 解析來源，您會需要使用 S3 媒體保留單元，以達到所需的效能。 如果您有 4k 內容超過 S3 媒體保留單元的 260 GB 限制，與我們連絡amshelp@microsoft.com取得潛在防護功能，以支援您的案例。

|媒體保留單元類型   |輸入的大小的上限 (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> 這個數字包括佇列、已完成、作用中和已取消的工作。 不包含已刪除的工作。 

您的帳戶中任何超過 90 天的工作記錄，都會自動刪除，即使記錄總數低於配額上限亦然。 

<sup>3</sup> live 的輸出上建立啟動和停止時刪除。

<sup>4</sup> 儲存體帳戶必須來自相同的 Azure 訂用帳戶。

<sup>5</sup>時使用自訂[Streaming 原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)，您應該設計您的媒體服務帳戶的一組有限的這類原則，並重新將它們用於您 StreamingLocators 時相同的加密選項，以及通訊協定所需。 不建議您對每個串流定位器建立新的串流原則。

<sup>6</sup>串流定位器並非設計來管理每位使用者存取控制。 若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 方案。

## <a name="support-ticket"></a>支援票證

如果是不固定的資源，您可以建立[支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，要求增加配額。 請在要求中包含所需的配額變更、使用案例情況及所需區域的詳細資訊。 <br/>**請勿**為了嘗試取得更高的限制而建立其他 Azure 媒體服務。

## <a name="next-steps"></a>後續步驟

[概觀](media-services-overview.md)
