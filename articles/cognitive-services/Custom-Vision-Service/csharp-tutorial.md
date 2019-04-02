---
title: 快速入門：使用適用於 C# 的自訂視覺 SDK 建立影像分類專案
titlesuffix: Azure Cognitive Services
description: 建立專案、新增標籤、上傳影像、為您的專案定型，以及使用 .NET SDK 和 C# 進行預測。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 9b6d01908265791a83ee311375fa50fcca995f79
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472716"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>快速入門：使用自訂視覺 .NET SDK 建立影像分類專案

本文提供資訊和範例程式碼，可協助您開始使用自訂視覺 SDK 與 C# 來建置影像分類模型。 建立它之後，您就可以新增標記、上傳影像、為專案定型、取得專案的預設預測端點 URL，並使用端點以程式設計方式測試影像。 請使用此範例作為範本來建置您自己的 .NET 應用程式。 如果您想要進行「不用」程式碼來建置及使用分類模型的程序，請改為參閱[以瀏覽器為基礎的指引](getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>必要條件

- [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 的任何版本

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>取得自訂視覺 SDK 與程式碼範例

若要撰寫使用自訂視覺的 .NET 應用程式，您需要有自訂視覺 NuGet 套件。 這些套件包含在您將會下載的專案範例內，但您可以在此個別存取這些套件。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

複製或下載[認知服務 .NET 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)專案。 在 Visual Studio 中瀏覽至 **CustomVision/ImageClassification** 資料夾並開啟 _ImageClassification.csproj_。

此 Visual Studio 專案會建立名為__我的新專案__的新自訂視覺專案，而此專案可透過[自訂視覺網站](https://customvision.ai/)來存取。 然後上傳影像以便為分類器定型並加以測試。 在此專案中，分類器會用來判斷影像是__鐵杉__還是__日本櫻__。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>了解程式碼

開啟 _Program.cs_ 檔案，並檢查程式碼。 在 **Main** 方法的適當定義中插入您的訂用帳戶金鑰。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

端點參數應指向包含自訂視覺資源的 Azure 資源群組建立所在的區域。 此範例中我們會假設在美國中南部區域，並使用：

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

以下幾行程式碼會執行專案的主要功能。

### <a name="create-a-new-custom-vision-service-project"></a>建立新的自訂視覺服務專案

所建立的專案會顯示在您稍早瀏覽過的[自訂視覺網站](https://customvision.ai/)上。 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>在專案中建立標記

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>上傳和標記影像

此專案的影像會包含在其中。 它們會在 _Program.cs_ 的 **LoadImagesFromDisk** 方法中受到參考。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>訓練分類器並發佈

此程式碼會在專案中建立第一個反覆項目，然後將該反覆項目發佈至預測端點。 提供給已發佈反覆項目的名稱可用來傳送預測要求。 反覆項目要等到發佈後才可在預測端點中使用。

```csharp
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>設定預測端點

預測端點可作為參考供您用來提交影像給目前的模型，並取得分類預測。

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>將影像提交至預設預測端點

在此指令碼中，會以 **LoadImagesFromDisk** 方法載入測試影像，且會在主控台中顯示模型的預測輸出。

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>執行應用程式

應用程式在執行時，應會開啟主控台視窗並寫入下列輸出：

```
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

接著，您可以確認測試影像 (位於 **Images/Test/** 中) 是否已正確加上標記。 按任意鍵以結束應用程式。 您也可以返回[自訂視覺網站](https://customvision.ai)，然後查看新建立專案的目前狀態。

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>後續步驟

現在您已經知道如何在程式碼中完成影像分類程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。

> [!div class="nextstepaction"]
> [測試和重新定型模型](test-your-model.md)
