---
title: 快速入門：使用適用於 Java 的自訂視覺 SDK 建立物件偵測專案
titlesuffix: Azure Cognitive Services
description: 建立專案、新增標籤、上傳影像、為您的專案定型，並使用 Java SDK 來偵測物件。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: 9ddb882658551115b05df3820e70a6b6684d563b
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604040"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>快速入門：使用適用於 Java 的自訂視覺 SDK 建立物件偵測專案

本文提供資訊和範例程式碼，可協助您開始使用自訂視覺 SDK 與 Java 來建置物件偵測模型。 建立它之後，您就可以新增標記的區域、上傳影像、為專案定型、取得專案的預設預測端點 URL，並使用端點以程式設計方式測試影像。 請使用此範例作為範本來建置您自己的 Java 應用程式。

## <a name="prerequisites"></a>必要條件

- 您選擇的 Java IDE
- 已安裝 [JDK 7 或 8](https://aka.ms/azure-jdks)。
- 已安裝 Maven

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>取得自訂視覺 SDK 與程式碼範例

若要撰寫使用自訂視覺的 Java 應用程式，您需要有自訂視覺 Maven 套件。 這些套件包含在您將會下載的專案範例內，但您可以在此個別存取這些套件。

您可以從 Maven 中央存放庫安裝自訂視覺 SDK：
- [定型 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [預測 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

複製或下載[認知服務 Java SDK 範例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master)專案。 瀏覽至 **Vision/CustomVision/** 資料夾。

此 Java 專案會建立名為 __範例 Java OD 專案__的新自訂視覺物件偵測專案，而此專案可透過[自訂視覺網站](https://customvision.ai/)來存取。 然後上傳影像以便為分類器定型並加以測試。 在此專案中，分類器會用來判斷影像是「鐵杉」  還是「日本櫻」  。

[!INCLUDE [get-keys](includes/get-keys.md)]

此程式會設定為將金鑰資料儲存為環境變數。 在 PowerShell 中瀏覽至 **Vision/CustomVision** 資料夾，以設定這些變數。 然後輸入命令：

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>了解程式碼

在 Java IDE 中載入 `Vision/CustomVision` 專案，然後開啟 _CustomVisionSamples.java_ 檔案。 尋找 **runSample** 方法，並將 **ImageClassification_Sample** 方法呼叫註解化 &mdash; 此方法會執行影像分類案例，但這不在本指南的討論範圍內。 **ObjectDetection_Sample** 方法會實作此快速入門的主要功能；瀏覽至其定義，然後檢查程式碼。 

### <a name="create-a-new-custom-vision-service-project"></a>建立新的自訂視覺服務專案

移至建立訓練用戶端和物件偵測專案的程式碼區塊。 所建立的專案會顯示在您稍早瀏覽過的[自訂視覺網站](https://customvision.ai/)上。 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>將標記新增到您的專案

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>上傳和標記影像

為物件偵測專案中的影像加上標記時，您必須使用標準化座標來識別每個加上標記的物件所屬的區域。 移至 `regionMap` 對應的定義。 此程式碼會為每個範例影像及其已加上標記的區域建立關聯。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

然後，請跳至將影像新增至專案的程式碼區塊。 系統會從專案的 **src/main/resources** 的資料夾讀取影像，並將其連同適當的標記和區域座標上傳至服務。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

先前的程式碼片段利用兩個協助程式函式，將影像擷取為資源串流，然後上傳到服務。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project-and-publish"></a>訓練專案並發佈

此程式碼會在專案中建立第一個反覆項目，然後將該反覆項目發佈至預測端點。 提供給已發佈反覆項目的名稱可用來傳送預測要求。 反覆項目要等到發佈後才可在預測端點中使用。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>使用預測端點

在此以 `predictor` 物件所表示的預測端點，可作為參考供您用來提交影像給目前的模型，並取得分類預測。 在此範例中，`predictor` 會在其他地方使用預測金鑰環境變數加以定義。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>執行應用程式

若要使用 Maven 編譯和執行解決方案，請在 PowerShell 中於專案目錄執行下列命令：

```powershell
mvn compile exec:java
```

在主控台輸出中檢視記錄和預測結果。 接著，您可以確認測試影像是否已正確加上標記，以及偵測的區域是否正確。

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已了解如何在程式碼中完成物件偵測程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。 下列指南會處理影像分類，但其原則類似於物件偵測。

> [!div class="nextstepaction"]
> [測試和重新定型模型](test-your-model.md)
