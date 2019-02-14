---
title: 快速入門：使用 C# 分析令人反感的影像 - Content Moderator
titlesuffix: Azure Cognitive Services
description: 如何使用 Content Moderator SDK for .NET 分析各種令人反感的影像內容
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: bd281191deb1a82d4b1b5247a011d9867f0c9bc4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879287"
---
# <a name="quickstart-analyze-image-content-for-objectionable-material-in-c"></a>快速入門：使用 C# 分析影像內容中的不當題材

本文提供可協助您開始使用 [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 的資訊和程式碼範例。 您將了解如何掃描成人或猥褻內容、可擷取的文字和人臉，以仲裁可能令人反感的內容。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

## <a name="prerequisites"></a>必要條件

- Content Moderator 訂用帳戶金鑰。 請依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱 Content Moderator 並取得金鑰。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)


> [!NOTE]
> 本指南將使用免費層的 Content Moderator 訂用帳戶。 若想了解各個訂用帳戶層級所提供的功能，請參閱[定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)頁面。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

1. 在 Visual Studio 中建立新的**主控台應用程式 (.NET Framework)** 專案，並將其命名為 **ImageModeration**。 
1. 如果您的解決方案中有其他專案，請選取此專案作為單一啟始專案。
1. 取得必要的 NuGet 套件。 以滑鼠右鍵按一下 [方案總管] 中的專案，並選取 [管理 NuGet 套件]，然後尋找並安裝下列套件：
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-image-moderation-code"></a>新增影像仲裁程式碼

接下來，您應將本指南中的程式碼複製並貼到您的專案中，以實作基本內容仲裁案例。

### <a name="include-namespaces"></a>包含命名空間

在 *Program.cs* 檔案的頂端新增下列 `using` 陳述式。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>建立 Content Moderator 用戶端

在您的 *Program.cs* 檔案中新增下列程式碼，為您的訂用帳戶建立 Content Moderator 用戶端提供者。 在相同命名空間中的 **Program** 類別旁新增程式碼。 您必須以區域識別碼和訂用帳戶金鑰的值更新 **AzureRegion** 和 **CMSubscriptionKey** 欄位。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=84-107)]


### <a name="set-up-input-and-output-targets"></a>設定輸入和輸出目標

將下列靜態欄位新增至 _Program.cs_ 中的 **Program** 類別。 這些項目會指定輸入影像內容和輸出 JSON 內容的檔案。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=49-53)]

您必須建立 *_ImageFiles.txt* 輸入檔，並據以更新其路徑 (相對於執行目錄的相對路徑)。 開啟 _ImageFiles.txt_，並新增要仲裁之影像的 URL。 本快速入門會以下列 URL 作為範例輸入。

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>建立用來處理結果的類別

在 *Program.cs* 中，將下列程式碼新增至相同命名空間中的 **Program** 類別旁。 您將使用此類別的執行個體，為每個審核的影像記錄仲裁結果。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=109-124)]


### <a name="define-the-image-evaluation-method"></a>定義影像評估方法

將下列方法新增至 **Program** 類別。 此方法會以三種不同的方式評估單一影像，並傳回評估結果。 若要深入了解個別作業的功用，請進入[後續步驟](#next-steps)一節中的連結。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=55-81)]

### <a name="load-the-input-images"></a>載入輸入影像

在 **Program** 類別中對 **Main** 方法新增下列程式碼。 這會設定程式，使其為輸入檔中的每個影像 URL 擷取評估資料。 接著會將此資料寫入至單一輸出檔案。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=17-46)]

## <a name="run-the-program"></a>執行程式

程式會將 JSON 字串資料寫入至 _ModerationOutput.json_ 檔案。 本快速入門中使用的範例影像會產生下列輸出。 請注意，每個影像都會有 `ImageModeration`、`FaceDetection` 和 `TextDetection` 的三個不同區段，對應於 **EvaluateImage** 方法中的三個 API 呼叫。

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已開發一個簡單的 .NET 應用程式，會使用 Content Moderator 服務傳回與指定的影像範例有關的資訊。 接下來請深入了解不同旗標和分類的意義，以便決定您所需的資料和應用程式應如何加以處理。

> [!div class="nextstepaction"]
> [影像仲裁指南](image-moderation-api.md)
