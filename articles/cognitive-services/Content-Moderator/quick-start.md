---
title: Azure Content Moderator 入門 | Microsoft Docs
description: 如何開始使用 Azure Content Moderator。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/15/2018
ms.author: sajagtap
ms.openlocfilehash: ae4333047ebd95733c7baaed0323a0c2c477d323
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368631"
---
# <a name="get-started-with-content-moderator"></a>開始使用 Content Moderator

您可透過下列方式開始使用 Content Moderator API 和檢閱工具：

- [開始使用審核工具](#start-with-the-review-tool)以建立 API 金鑰和檢閱小組。 探索審核工具及了解如何使用 Content Moderator API 進行整合。
- 在 Azure 入口網站中[訂閱 Content Moderator](#start-with-the-apis)。 您仍然需要上線登入，才能建立檢閱小組。
- 透過方便使用的設計工具，[使用流程連接器和範本](https://flow.microsoft.com/connectors/shared_cognitiveservicescontentmoderator/content-moderator/)來嘗試各種不同的整合。

不論您選擇什麼選項，請參閱[管理認證](review-tool-user-guide/credentials.md)文章來尋找您的 API 認證。

## <a name="start-with-the-review-tool"></a>從審核工具開始
在 Content Moderator 審核工具網站上[註冊](http://contentmoderator.cognitive.microsoft.com/)。

![Content Moderator 首頁](images/homepage.PNG)

### <a name="create-a-review-team"></a>建立檢閱小組
指定小組的名稱。 如果您想邀請同事，可以輸入他們的電子郵件地址。

![邀請小組成員](images/QuickStart-2-small.png)

### <a name="upload-images-or-enter-text"></a>上傳影像或輸入文字
按一下 [嘗試] > [影像] 或 [嘗試] > [文字]。 上傳最多五個範例影像或輸入範例文字以用於審核。

![嘗試影像或文字審核](images/tryimagesortext.png)

### <a name="submit-for-automated-moderation"></a>提交以進行自動審核
提交您的內容以進行自動審核。 就內部而言，審核工具會呼叫審核 API 來掃描您的內容。 掃描完成之後，您會看到有結果等候檢閱的通知訊息。

![審核檔案](images/submitted.png)

### <a name="review-and-confirm-results"></a>檢閱並確認結果
檢閱自動審核標記，並視需要進行變更，然後使用 [下一步] 按鈕即可提交。 如您的商務應用程式呼叫 Moderator API，加上標記的內容就會開始排入佇列，準備讓人力檢閱小組進行檢閱。 您可以使用此方法快速檢閱大量內容。

![檢閱結果](images/reviewresults.png)

了解如何使用所有[審核工具的功能](Review-Tool-User-Guide/human-in-the-loop.md)，或繼續進行下一節以了解 API。 請略過註冊步驟，因為您已在審核工具中佈建 API 金鑰，如[管理認證](review-tool-user-guide/credentials.md)一文所示。

### <a name="use-the-apis"></a>使用 API

現在您已探索過內容審核和審核工具的體驗，接著可了解如何將 Content Moderator 與您的商務應用程式整合。 使用下一節來深入探索和快速追蹤您對 SDK 與範例的了解。

## <a name="start-with-the-apis"></a>從 API 開始

在 Azure 入口網站中[訂閱 Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)。 開始使用下列其中一個 API：

### <a name="image-moderation"></a>影像仲裁

開始使用 [API 主控台](try-image-api.md)或使用 [.NET 快速入門](image-moderation-quickstart-dotnet.md)來掃描影像，並使用標記、信賴分數及其他擷取資訊來偵測潛在的成人和不雅內容。

### <a name="text-moderation"></a>文字仲裁

開始使用 [API 主控台](try-text-api.md)或使用 [.NET 快速入門](text-moderation-quickstart-dotnet.md) 來掃描內容，以找出不雅內容、機器輔助的不必要文字分類 (預覽) 及個人識別資訊 (PII)。 


### <a name="video-moderation"></a>影片仲裁

開始使用 [.NET 快速入門](video-moderation-api.md)來掃描影片，並偵測潛在的成人和不雅內容。 


### <a name="review-apis"></a>檢閱 API

從選擇作業、檢閱及工作流程 API 開始。

- [作業 API](try-review-api-job.md) 會使用審核 API 掃描您的內容，並在檢閱工具中產生檢閱。 
- [檢閱 API](try-review-api-review.md) 會直接為人工審核者建立影像、文字或影片檢閱，無須先掃描內容。 
- [工作流程 API](try-review-api-workflow.md) 會建立、更新並取得您小組建立的自訂工作流程詳細資料。

## <a name="next-steps"></a>後續步驟

從[影像審核 API](image-moderation-api.md) 開始，深入了解內容審核。
