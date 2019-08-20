---
title: 快速入門：使用適用於 C# 的自訂視覺 SDK 建立物件偵測專案
titleSuffix: Azure Cognitive Services
description: 建立專案、新增標籤、上傳影像、為您的專案定型，並使用 .NET SDK 和 C# 來偵測物件。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 34b814e854a1576fcf55d14ddc5ac213d8f87070
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945153"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>快速入門：使用自訂視覺 .NET SDK 建立物件偵測專案

本文提供資訊和範例程式碼，可協助您開始使用自訂視覺 SDK 與 C# 來建置物件偵測模型。 建立它之後，您就可以新增標記的區域、上傳影像、為專案定型、取得專案的預設預測端點 URL，並使用端點以程式設計方式測試影像。 請使用此範例作為範本來建置您自己的 .NET 應用程式。 

## <a name="prerequisites"></a>必要條件

- [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 的任何版本

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>取得自訂視覺 SDK 與程式碼範例

若要撰寫使用自訂視覺的 .NET 應用程式，您需要有自訂視覺 NuGet 套件。 這些套件包含在您將會下載的專案範例內，但您可以在此個別存取這些套件。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

複製或下載[認知服務 .NET 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)專案。 在 Visual Studio 中瀏覽至「CustomVision/ObjectDetection」  資料夾並開啟_ObjectDetection.csproj_。

此 Visual Studio 專案會建立名為 __My New Project__ 的新自訂視覺專案，而此專案可透過[自訂視覺網站](https://customvision.ai/)來存取。 接著，它會上傳影像，以針對物件偵測模型進行定型及測試。 在此專案中，將會為模型定型以偵測影像中的叉子和剪刀。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>了解程式碼

開啟 _Program.cs_ 檔案，並檢查程式碼。 在 **Main** 方法的適當定義中插入您的訂用帳戶金鑰。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

端點參數應指向包含自訂視覺資源的 Azure 資源群組建立所在的區域。 此範例中我們會假設在美國中南部區域，並使用：

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

### <a name="create-a-new-custom-vision-service-project"></a>建立新的自訂視覺服務專案

下一段的程式碼會建立物件偵測專案。 所建立的專案會顯示在您稍早瀏覽過的[自訂視覺網站](https://customvision.ai/)上。 當您建立專案時，請參閱 [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) 方法來指定其他選項 (如[建立偵測器](get-started-build-detector.md) Web 入口網站指南中所述)。  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>將標記新增至專案

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>上傳和標記影像

為物件偵測專案中的影像加上標記時，您必須使用標準化座標來識別每個加上標記的物件所屬的區域。 下列程式碼會為每個範例影像及其已加上標記的區域建立關聯。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

然後，此關聯對應會用來上傳每個範例影像及其區域座標。 您最多可以在單一批次中上傳 64 個影像。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

至此，所有範例影像均已上傳，且各有其標記 (**叉子**或**剪刀**) 以及與該標記相關聯的像素矩形。

### <a name="train-the-project"></a>為專案定型

此程式碼會建立專案中的第一個訓練反覆項目。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="publish-the-current-iteration"></a>發佈目前的反覆項目

提供給已發佈反覆項目的名稱可用來傳送預測要求。 反覆項目要等到發佈後才可在預測端點中使用。

```csharp
// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="create-a-prediction-endpoint"></a>建立預測端點

```csharp
// Create a prediction endpoint, passing in the obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="use-the-prediction-endpoint"></a>使用預測端點

這部分的指令碼會載入測試影像、查詢模型端點，並將預測資料輸出至主控台。

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var imageFile = Path.Combine("Images", "test", "test_image.jpg");
using (var stream = File.OpenRead(imageFile))
{
        var result = endpoint.DetectImage(project.Id, publishedModelName, stream);

        // Loop over each prediction and write out the results
        foreach (var c in result.Predictions)
        {
                Console.WriteLine($"\t{c.TagName}: {c.Probability:P1} [ {c.BoundingBox.Left}, {c.BoundingBox.Top}, {c.BoundingBox.Width}, {c.BoundingBox.Height} ]");
        }
}
```

## <a name="run-the-application"></a>執行應用程式

應用程式在執行時，應會開啟主控台視窗並寫入下列輸出：

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

接著，您可以確認測試影像 (位於 **Images/Test/** 中) 是否已正確加上標記，以及偵測的區域是否正確。 此時，您可以按任意鍵以結束應用程式。

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已了解如何在程式碼中完成物件偵測程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。 下列指南會處理影像分類，但其原則類似於物件偵測。

> [!div class="nextstepaction"]
> [測試和重新定型模型](test-your-model.md)
