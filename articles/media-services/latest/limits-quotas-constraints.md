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
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 2a530d6a1a447ccde448259623da6faeaa6a4d72
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598354"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure 媒體服務 v3 配額和限制

本文描述 Azure 媒體服務 v3 的配額和限制。

| 資源 | 預設限制 | 
| --- | --- | 
| 每個 Azure 媒體服務帳戶的資產 | 1,000,000|
| 動態資訊清單篩選條件|100|
| 每個工作的工作輸入 | 50 (固定)|
| 每個工作的工作輸出 | 20 (固定) |
| 轉換中的 Transformoutputs 優先順序 | 20 (固定) |
| 每個 JobInput 的檔案|10 (固定)|
| 檔案大小| 在某些情況下，對於媒體服務中支援處理的檔案大小上限有所限制。 <sup>(1)</sup> |
| 每個媒體服務帳戶的工作 | 500,000 <sup>(2)</sup> (固定)|
| 每個媒體服務帳戶的即時事件 |5|
| 單一訂用帳戶的媒體服務帳戶 | 25 (固定) |
| 每個即時事件的即時輸出 |3 <sup>（3）</sup> |
| 即時輸出持續時間上限 | 25小時 |
| 儲存體帳戶 | 100<sup>(4)</sup> (固定) |
| 每個媒體服務帳戶的串流端點 (已停止或執行中)|2（固定）|
| 串流原則 | 100 <sup>（5）</sup> |
| 每個媒體服務帳戶的轉換 | 100 (固定)|
| 一次與資產相關聯的唯一串流定位器 | 100<sup>（6）</sup> （固定） |
| 每個內容金鑰原則的選項 |30 | 
| 針對每個帳戶媒體服務金鑰傳遞服務上的每個 DRM 類型，每月的授權|1,000,000|

<sup>1</sup> 單一 blob 支援的大小上限目前在 Azure Blob 儲存體是最多 5 TB。 根據服務所使用的 VM 大小，媒體服務中會套用額外的限制。 大小限制適用于您上傳的檔案，以及因媒體服務處理（編碼或分析）而產生的檔案。 如果原始程式檔超過 260 GB，您的工作可能會失敗。 

下表顯示媒體保留單元 S1、S2 和 S3 的限制。 如果您的原始程式檔大於資料表中所定義的限制，則編碼工作會失敗。 如果您編碼長時間的4K 解析來源，就必須使用 S3 媒體保留單元來達到所需的效能。 如果您的4K 內容大於 S3 媒體保留單元的 260 GB 限制，請開啟支援票證。

|媒體保留單元類型   |輸入大小上限（GB）|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> 這個數字包括佇列、已完成、作用中和已取消的工作。 不包含已刪除的工作。 

您的帳戶中任何超過 90 天的工作記錄，都會自動刪除，即使記錄總數低於配額上限亦然。 

<sup>3</sup>即時輸出會在建立時開始，並在刪除時停止。

<sup>4</sup> 儲存體帳戶必須來自相同的 Azure 訂用帳戶。

<sup>5</sup>使用自訂[串流原則](https://docs.microsoft.com/rest/api/media/streamingpolicies)時，您應該為媒體服務帳戶設計一組受限的這類原則，並且在需要相同的加密選項和通訊協定時，針對您的 streaminglocator 重複使用它們。 不建議您對每個串流定位器建立新的串流原則。

<sup>6</sup>串流定位器並非設計來管理每個使用者的存取控制。 若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 方案。

## <a name="support-ticket"></a>支援票證

如果是不固定的資源，您可以建立[支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，要求增加配額。 請在要求中包含所需的配額變更、使用案例情況及所需區域的詳細資訊。 <br/>**請勿**為了嘗試取得更高的限制而建立其他 Azure 媒體服務。

## <a name="next-steps"></a>後續步驟

[Overview](media-services-overview.md)
