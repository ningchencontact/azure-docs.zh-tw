---
title: 使用 REST API 主控台-Content Moderator 審核工作
titlesuffix: Azure Cognitive Services
description: 使用檢閱 API 的作業操作來對 Azure Content Moderator 中的影像或文字內容起始端對端內容審核。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: 7827cee2af2dfc0c1fddc407c1d146dc9a66c514
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756086"
---
# <a name="define-and-use-moderation-jobs-rest"></a>定義和使用仲裁作業 (REST)

仲裁作業可做為一種包裝函式的內容仲裁、 工作流程及檢閱功能。 本指南將說明如何使用 REST Api 作業來起始，並檢查內容仲裁作業。 一旦您了解 Api 的結構，您可以輕鬆地移植這些呼叫任何 REST 相容平台。

## <a name="prerequisites"></a>必要條件

- 登入或建立帳戶，在內容仲裁[審核工具](https://contentmoderator.cognitive.microsoft.com/)站台。
- （選擇性）[定義的自訂工作流程](./Review-Tool-User-Guide/Workflows.md)以搭配您的工作; 您也可以使用預設的工作流程。

## <a name="create-a-job"></a>建立工作

若要建立仲裁作業時，請前往[作業-建立](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)API 參考頁面，然後選取您的主要區域 按鈕 (您可以在端點 URL 上找到**認證**頁面[檢閱工具](https://contentmoderator.cognitive.microsoft.com/))。 這會啟動 API 主控台中，您可以輕鬆地建構並執行 REST API 呼叫。

![作業-建立頁面地區選取項目](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>輸入 REST 呼叫的參數

輸入下列值來建構 REST 呼叫：

- **teamName**：當您設定時，您建立 「 小組識別碼您[審核工具](https://contentmoderator.cognitive.microsoft.com/)帳戶 (位於**識別碼**檢閱工具的 [認證] 畫面上的欄位)。
- **ContentType**:這可以是 「 映像 」、 「 文字 」 或 「 視訊 」。
- **ContentId**：自訂識別項字串。 這個字串會傳遞至 API 並透過回呼傳回。 它可用於與仲裁作業的結果產生關聯的內部識別項或中繼資料。
- **workflowname**:您先前建立的工作流程 （或預設工作流程的 「 預設 」） 的名稱。
- **CallbackEndpoint**:（選擇性）若要檢閱完成時接收回呼資訊 URL。
- **Ocp-Apim-Subscription-Key**：您的內容仲裁者金鑰。 您可以找到這**設定**索引標籤[審核工具](https://contentmoderator.cognitive.microsoft.com)。

### <a name="fill-in-the-request-body"></a>要求主體中填滿

您的 REST 呼叫的主體會包含一個欄位**ContentValue**。 如果您仲裁文字、 未經處理的文字內容中貼上或輸入影像或影片 URL，如果您要調節影像/影片。 您可以使用下列的範例影像 URL: [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![[作業 - 建立] 主控台查詢參數、標頭和要求本文方塊](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>提交您的要求

選取 [傳送]。 如果作業成功，**回應狀態**是`200 OK`，而**回應內容**方塊會顯示作業的識別碼。 複製此識別碼以在下列步驟中使用。

![[審查 - 建立] 主控台的 [回應內容] 方塊會顯示審查識別碼](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>取得作業狀態

若要取得 [狀態] 和 [執行中或已完成作業的詳細資訊，請前往[作業-取得](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3)API 參考頁面，然後選取您的區域] 按鈕 （取決於您的金鑰所在的區域）。

![作業-Get 地區選取項目](images/test-drive-region.png)

輸入上述節所述的 REST 呼叫參數。 此步驟中，如**JobId**是建立工作時所收到的唯一 ID 字串。 選取 [傳送]。 如果作業成功，**回應狀態**是`200 OK`，而**回應內容**方塊會顯示作業，以 JSON 格式，如下所示：

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![作業-取得 REST 呼叫回應](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>檢查新 review(s)

如果您內容的工作並檢閱的建立，您可以檢視它在[審核工具](https://contentmoderator.cognitive.microsoft.com)。 選取 **檢閱** > **映像**/**文字**/**影片**（取決於您內容項目使用）。 內容應該會出現，可供人工審核。 人力仲裁者檢閱自動指派的標記和預測資料，並且提交的最終仲裁決策後，作業 API 提交所有的這項資訊來指定的回呼端點的端點。

## <a name="next-steps"></a>後續步驟

在本指南中，您已了解如何建立及查詢使用 REST API 的內容仲裁作業。 接下來，將工作整合的端對端審核案例，例如[電子商務仲裁](./ecommerce-retail-catalog-moderation.md)教學課程。