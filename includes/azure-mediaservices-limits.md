---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: b275a86f8fd35c43865fd920d1bfc9994a796a9c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803561"
---
>[!NOTE]
>如需不固定的資源，開啟支援票證以要求增加配額。 請勿建立其他的 Azure 媒體服務帳戶在嘗試取得較高的限制。

| 資源 | 預設限制 | 
| --- | --- | 
| 單一訂用帳戶中的 azure 媒體服務帳戶 | 25 (固定) |
| 媒體保留單元，每個媒體服務帳戶 |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| 每個媒體服務帳戶的工作 | 50,000<sup>2</sup> |
| 每個作業的鏈結工作 | 30 (固定) |
| 每個媒體服務帳戶的資產 | 1,000,000|
| 每个任务的资产数 | 50 |
| 每個作業的資產 | 100 |
| 一次與資產相關聯的唯一定位器 | 5<sup>4</sup> |
| 每個媒體服務帳戶的直播頻道 |5|
| 每个频道的停止状态节目数 |50|
| 每个频道的运行状态节目数 |3|
| 串流端點已停止，或執行每個媒體服務帳戶|2|
| 每個串流端點的資料流單位  |10 |
| 儲存體帳戶 | 1,000<sup>5</sup> (fixed) |
| 原則 | 1,000,000<sup>6</sup> |
| 檔案大小| 在某些情況下，沒有限制對於媒體服務中支援處理的最大檔案大小。<sup>7</sup> |

<sup>1</sup>如果您變更類型，例如從 S2 到 S1，保留的單元最大值限制會重設。

<sup>2</sup>這個數目包括佇列、 完成、 作用中和已取消的作業。 它不包含已刪除的工作。 您可以使用，以刪除舊工作**IJob.Delete**或**刪除**HTTP 要求。

截至 2017 年 4 月 1 日起，任何超過 90 天您帳戶中的作業記錄會自動刪除，以及其相關聯的工作記錄。 即使記錄總數低於配額上限，就會發生自動刪除。 若要封存的作業和工作的資訊，請使用 程式碼中所述[使用媒體服務.NET SDK 管理資產](../articles/media-services/previous/media-services-dotnet-manage-entities.md)。

<sup>3</sup>要求清單作業實體時，最多 1000 個作業會傳回每個要求。 要追蹤的所有提交的作業，使用 top 或略過查詢中所述[OData 系統查詢選項](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))。

<sup>4</sup>定位器不設計來管理每位使用者存取控制。 若要給予個別使用者不同的存取權限，請使用數位版權管理 (DRM) 解決方案。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure Media Services 保護內容](../articles/media-services/previous/media-services-content-protection-overview.md)。

<sup>5</sup>的儲存體帳戶必須是相同的 Azure 訂用帳戶。

<sup>6</sup>對於不同的媒體服務原則的 1,000,000 個原則的限制。 範例是針對 Locator 原則或 ContentKeyAuthorizationPolicy。 

>[!NOTE]
> 如果您一律使用相同的天數和存取權限，使用相同的原則識別碼。 資訊和範例，請參閱[使用媒體服務.NET SDK 管理資產](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)。

<sup>7</sup>如果您將內容上傳至透過其中一個服務中的媒體處理器加以處理的媒體服務中的資產時，請注意支援的最大檔案大小。 資產包括像媒體編碼器標準編碼器和 Media Encoder Premium Workflow 或分析引擎，例如臉部偵測器。

最多 5 TB 的 Azure Blob 儲存體中的目前支援單一 blob 的大小上限。 其他的限制適用於服務所使用的 VM 大小所根據的媒體服務中。 下表會顯示上媒體保留單位 S1、 S2 和 S3 的限制。 如果您的來源檔案大於資料表中定義的限制，編碼作業將會失敗。 如果編碼的持續時間較長的 4k 解析來源，您會需要使用 S3 媒體保留單元，以達到所需的效能。 如果您有 4k 內容超過 S3 媒體保留單元的 260 GB 限制，與我們連絡amshelp@microsoft.com取得潛在防護功能，以支援您的案例。

| 媒體保留單元類型 | 輸入的大小的上限 (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
