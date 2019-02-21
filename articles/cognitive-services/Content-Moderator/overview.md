---
title: 什麼是 Azure Content Moderator？
titlesuffix: Azure Cognitive Services
description: 了解如何使用 Content Moderator 對使用者產生的內容追蹤、標幟、評估及篩選其中的不當內容。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 7a6424921b869428d1dbeffadd68e173a32d8821
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455241"
---
# <a name="what-is-azure-content-moderator"></a>什麼是 Azure Content Moderator？

Azure Content Moderator API 是一項認知服務，可檢查文字、影像和視訊內容是否含有可能具冒犯意味、有風險或不當的資料。 找到這類資料時，服務會對內容套用適當的標籤 (旗標)。 加上旗標的內容隨後可由您的應用程式處理，以遵循法規或維護使用者應有的環境。 請參閱 [Content Moderator API](#content-moderator-apis) 一節，以深入了解不同內容旗標的意義。

## <a name="where-it-is-used"></a>使用位置

以下是軟體開發人員或小組可能使用 Content Moderator 的幾個案例：

- 對產品目錄和使用者產生的其他內容進行仲裁的線上市集
- 對使用者產生的遊戲成品和聊天室進行仲裁的遊戲公司
- 對使用者新增的影像、文字和影片進行仲裁的社交傳訊平台
- 對其內容實作集中式仲裁的企業媒體公司
- 為學生和授課者篩選掉不當內容的 K-12 教育解決方案提供者

## <a name="what-it-includes"></a>包含內容

Content Moderator 服務由數個可透過 REST 呼叫和 .NET SDK 使用的 Web 服務 API 所組成。 此外也包含人工審核工具，可讓人工審核者輔助服務，以及改善或微調其仲裁功能。

![顯示仲裁 API、審核 API 和人工審核工具的 Content Moderator 區塊圖](images/content-moderator-block-diagram.png)

### <a name="content-moderator-apis"></a>Content Moderator API

Content Moderator 服務包含適用於下列案例的 API。

| 動作 | 說明 |
| ------ | ----------- |
|[**文字仲裁**](text-moderation-api.md)| 掃描文字中具冒犯性的內容、明顯色情或性暗示內容、粗話，和個人識別資訊 (PII)。|
|[**自訂字詞清單**](try-terms-list-api.md)| 除了內建字詞外，也根據自訂字詞清單掃描文字。 使用自訂清單可根據您自己的內容原則來封鎖或允許內容。|  
|[**影像仲裁**](image-moderation-api.md)| 掃描影像中的成人或猥褻內容、使用光學字元辨識 (OCR) 功能偵測影像中的文字，以及偵測臉部。|
|[**自訂影像清單**](try-image-list-api.md)| 根據自訂影像清單掃描影像。 使用自訂影像清單，篩選掉重複出現、而您不想再次分類的內容執行個體。|
|[**影片仲裁**](video-moderation-api.md)| 掃描影片中的成人或猥褻內容，並傳回該內容的時間標記。|
|[**審核**](try-review-api-job.md)| 使用[作業](try-review-api-job.md)、[審核](try-review-api-review.md)與[工作流程](try-review-api-workflow.md)作業來建立及自動化審核工具的人機互動工作流程。 工作流程 API 尚無法透過 .NET SDK 來使用。|

### <a name="human-review-tool"></a>人工檢閱工具

Content Moderator 服務也包含以 Web 為基礎的[人工審核工具](Review-Tool-User-Guide/human-in-the-loop.md)。 

![Content Moderator 的人工審核工具首頁](images/homepage.PNG)

您可以使用審核 API，根據您指定的篩選條件來設定文字、影像和影片內容的小組審核。 然後，人工仲裁者可做出最終的仲裁決策。 人工輸入不會訓練服務，但在服務與人工審核小組的搭配運作下，開發人員將可在效率和精確度之間取得適當的平衡。

## <a name="data-privacy-and-security"></a>資料隱私權和安全性
和所有認知服務一樣，使用 Content Moderator 服務的開發人員應該要了解 Microsoft 對於客戶資料的政策。 請參閱 Microsoft 信任中心上的[認知服務頁面](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)，以進行深入了解。

## <a name="next-steps"></a>後續步驟

請遵循[在 Web 上試用 Content Moderator](quick-start.md) 中的指示，以開始使用 Content Moderator 服務。
