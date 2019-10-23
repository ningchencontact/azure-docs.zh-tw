---
title: 快速入門：在 C# 中分析文字內容 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 如何使用 Content Moderator SDK for .NET 分析各種令人反感的文字內容
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 40f73a6aabd3631bf2a2f8f1406d2ec9abd55e51
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757218"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>快速入門：使用 C# 分析文字內容中的不當題材

本文提供可協助您開始使用 [Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 的資訊和程式碼範例。 您將了解如何執行文字內容的字詞型篩選和分類，以仲裁可能令人反感的內容。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>必要條件
- Content Moderator 訂用帳戶金鑰。 請依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱 Content Moderator 並取得金鑰。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> 本指南將使用免費層的 Content Moderator 訂用帳戶。 若想了解各個訂用帳戶層級所提供的功能，請參閱[定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)頁面。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

1. 在 Visual Studio 中建立新的**主控台應用程式 (.NET Framework)** 專案，並將其命名為 **TextModeration**。 
1. 如果您的解決方案中有其他專案，請選取此專案作為單一啟始專案。
1. 取得必要的 NuGet 套件。 在 Solution Explorer 中以滑鼠右鍵按一下您的專案，然後選取 [管理 NuGet 套件]  。 接著，尋找並安裝 **`Microsoft.Azure.CognitiveServices.ContentModerator` 套件**。 或者，您也可以從解決方案目錄執行下列命令：

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator
```

## <a name="add-text-moderation-code"></a>新增文字仲裁程式碼

接下來，您應將本指南中的程式碼複製並貼到您的專案中，以實作基本內容仲裁案例。

### <a name="include-namespaces"></a>包含命名空間

在 *Program.cs* 檔案的頂端新增下列 `using` 陳述式。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>建立 Content Moderator 用戶端

在您的 *Program.cs* 檔案中新增下列程式碼，為您的訂用帳戶建立 Content Moderator 用戶端提供者。 在相同命名空間中的 **Program** 類別旁新增程式碼。 您必須以區域識別碼和訂用帳戶金鑰的值更新 **AzureRegion** 和 **CMSubscriptionKey** 欄位。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>設定輸入和輸出目標

將下列靜態欄位新增至 _Program.cs_ 中的 **Program** 類別。 這些欄位會指定輸入文字內容和輸出 JSON 內容的檔案。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

您必須建立 *TextFile.txt* 輸入檔，並更新其路徑 (相對於執行目錄的路徑)。 開啟 _TextFile.txt_ 並新增要仲裁的文字。 本快速入門會使用下列範例文字：

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>載入輸入文字

將以下程式碼新增至 **Main** 方法。 **ScreenText** 方法是基本作業。 其參數會指定將執行哪些內容仲裁作業。 在此範例中，方法會設定為：
- 偵測文字中可能的粗話。
- 將文字正規化並自動校正錯字。
- 偵測個人資料，例如美國和英國電話號碼、電子郵件地址及美國郵寄地址。
- 使用機器學習型模型將文字分成三種類別。

若要深入了解這些作業的功用，請進入[後續步驟](#next-steps)一節中的連結。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>執行程式

程式會將 JSON 字串資料寫入至 _TextModerationOutput.txt_ 檔案。 本快速入門中使用的範例文字會產生下列輸出：

```json
Autocorrect typos, check for matching terms, check for personal data, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已開發一個簡單的 .NET 應用程式，會使用 Content Moderator 服務傳回與指定的文字範例有關的資訊。 接下來請深入了解不同旗標和分類的意義，以便決定您所需的資料和應用程式應如何加以處理。

> [!div class="nextstepaction"]
> [文字仲裁指南](text-moderation-api.md)
