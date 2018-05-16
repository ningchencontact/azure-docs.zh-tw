---
title: Azure 媒體服務 v3 配額和限制 | Microsoft Docs
description: 本主題描述 Azure 媒體服務 v3 的配額和限制
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure 媒體服務 v3 配額和限制

本主題描述 Azure 媒體服務 v3 的配額和限制。

| 資源 | 預設限制 | 
| --- | --- | 
| 每個 Azure 媒體服務帳戶的資產 | 1,000,000|
| 每個工作的工作輸入 | 100 |
| 每個工作的工作輸出 | 30 (固定) |
| 檔案大小| 在某些情況下，對於媒體服務中支援處理的檔案大小上限有所限制。 <sup>(1)</sup> |
| 每個媒體服務帳戶的工作 | 50,000<sup>(2)</sup> |
| 每個媒體服務帳戶的 LiveEvents |5|
| 單一訂用帳戶的媒體服務帳戶 | 25 (固定) |
| StreamingPolicies | 1,000,000<sup>(3)</sup> |
| 每個 LiveEvent 其處於執行中狀態的 LiveOutputs |3|
| 每個 LiveEvent 其處於已停止狀態的 LiveOutputs |50|
| 儲存體帳戶 | 1,000<sup>(4)</sup> (固定) |
| 每個媒體服務其處於執行中狀態的串流端點|2|
| 每個媒體服務帳戶的轉換 | 20 |
| 一次與資產相關聯的唯一 StreamingLocator | 20<sup>(5)</sup> |
  
<sup>1</sup>單一 blob 支援的大小上限，目前在 Azure Blob 儲存體最多為 5 TB。 不過，其他的限制會以服務所使用的 VM 大小作為基礎套用在 Azure 媒體服務中。 如果原始程式檔超過 260 GB，您的工作可能會失敗。 如果您的 4K 內容超過 260 GB 限制，請來函寄到 amshelp@microsoft.com，我們將針對您的情況，提供解決的方法。

<sup>2</sup> 這個數字包括佇列、已完成、作用中和已取消的工作。 不包含已刪除的工作。 

您的帳戶中任何超過 90 天的工作記錄，都會自動刪除，即使記錄總數低於配額上限亦然。 

<sup>3</sup> 對於不同的媒體服務原則 (例如 StreamingLocator 原則或 ContentKeyAuthorizationPolicy)，有 100 萬個 StreamingPolicy 的限制。 

>[!NOTE]
> 如果您總是使用相同的天數 / 存取權限 / 等等，您應該使用相同的原則識別碼。 

<sup>4</sup> 儲存體帳戶必須來自相同的 Azure 訂用帳戶。

<sup>5</sup> StreamingLocators 並非設計來管理每個使用者的存取控制。 若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 方案。

## <a name="support-ticket"></a>支援票證

如果是不固定的資源，您可以建立[支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，要求增加配額。 請在要求中包含所需的配額變更、使用案例情況及所需區域的詳細資訊。 <br/>**請勿**為了嘗試取得更高的限制而建立其他 Azure 媒體服務。

## <a name="next-steps"></a>後續步驟

[概觀](media-services-overview.md)
