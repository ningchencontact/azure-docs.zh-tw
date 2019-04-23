---
title: 使用 REST API 主控台-Content Moderator 建立仲裁評論
titlesuffix: Azure Cognitive Services
description: 您可以使用 Azure 內容仲裁者檢閱 Api 來建立映像或文字的評論，讓您以人工審核。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 254269ccedc92b9dfc164cc4665a8a8513682773
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58882002"
---
# <a name="create-human-reviews-rest"></a>建立人工審核 (REST)

[檢閱](./review-api.md#reviews)儲存及顯示人力仲裁者，以評估的內容。 當使用者完成檢閱時，結果會傳送至指定的回呼端點。 本指南中，您將了解如何設定使用檢閱 REST Api 透過 API 主控台評論。 一旦您了解 Api 的結構，您可以輕鬆地移植這些呼叫任何 REST 相容平台。

## <a name="prerequisites"></a>必要條件

- 登入或建立帳戶，在內容仲裁[審核工具](https://contentmoderator.cognitive.microsoft.com/)站台。

## <a name="create-a-review"></a>建立檢閱

若要建立的檢閱，請前往**[檢閱-建立](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API 參考頁面，然後選取您的主要區域 按鈕 (您可以在端點 URL 上找到**認證**頁面[審核工具](https://contentmoderator.cognitive.microsoft.com/))。 這會啟動 API 主控台中，您可以輕鬆地建構並執行 REST API 呼叫。

![檢閱-Get 地區選取項目](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫的參數

輸入的值**teamName**，並**Ocp Apim-訂用帳戶金鑰**:

- **teamName**：當您設定時，您建立 「 小組識別碼您[審核工具](https://contentmoderator.cognitive.microsoft.com/)帳戶 (位於**識別碼**檢閱工具的 [認證] 畫面上的欄位)。
- **Ocp-Apim-Subscription-Key**：您的內容仲裁者金鑰。 您可以找到這**設定**索引標籤[審核工具](https://contentmoderator.cognitive.microsoft.com)。

### <a name="enter-a-review-definition"></a>輸入檢閱定義

編輯**要求本文** 方塊中輸入 JSON 要求具有下列欄位：

- **中繼資料**：要傳回給您的回呼端點的自訂索引鍵 / 值組。 如果索引鍵是簡短的程式碼中定義[審核工具](https://contentmoderator.cognitive.microsoft.com)，它會顯示為標記。
- **內容**:在映像和視訊內容的情況下，這是指向內容的 URL 字串。 如需文字內容，這是實際的文字字串。
- **ContentId**：自訂識別項字串。 這個字串會傳遞至 API 並透過回呼傳回。 它可用於與仲裁作業的結果產生關聯的內部識別項或中繼資料。
- **CallbackEndpoint**:（選擇性）若要檢閱完成時接收回呼資訊 URL。

預設要求主體會顯示的檢閱您可以建立不同類型的範例：

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>提交您的要求
  
選取 [傳送]。 如果作業成功，**回應狀態**是`200 OK`，而**回應內容**方塊會顯示此檢閱的識別碼。 複製此識別碼以在下列步驟中使用。

![[審查 - 建立] 主控台的 [回應內容] 方塊會顯示審查識別碼](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>檢查新的檢閱

在 [審核工具](https://contentmoderator.cognitive.microsoft.com)，選取**檢閱** > **映像**/**文字**/ **影片**（視內容而定您使用）。 您上傳的內容應該會出現，供人工審核。

![足球的審查工具影像](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>取得檢閱詳細資料

若要擷取現有的檢閱相關的詳細資訊，請前往[檢閱-取得](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2)API 參考頁面，然後選取您的區域 按鈕 （取決於您的金鑰所在的區域）。

![[工作流程 - 取得] 的區域選取項目](images/test-drive-region.png)

輸入上述節所述的 REST 呼叫參數。 此步驟中，如**reviewId**是建立檢閱時所收到的唯一 ID 字串。

![審查 - 建立主控台的取得結果](images/test-drive-review-3.PNG)
  
選取 [傳送]。 如果作業成功，**回應狀態**是`200 OK`，而**回應內容** 方塊中檢閱詳細資料以 JSON 格式顯示，如下所示：

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

記下回應中的下列欄位：

- **status**
- **reviewerResultTags**:任何標記已手動新增人工審核小組同時出現 (顯示**createdBy**欄位)。
- **中繼資料**：這會顯示一開始中檢閱，人工審核小組所做變更之前新增的標記。

## <a name="next-steps"></a>後續步驟

在本指南中，您已了解如何建立使用 REST API 的內容仲裁評論。 接下來，整合評論的端對端審核案例，例如[電子商務仲裁](./ecommerce-retail-catalog-moderation.md)教學課程。