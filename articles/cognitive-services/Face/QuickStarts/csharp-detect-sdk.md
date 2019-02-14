---
title: 快速入門：使用 Azure Face .NET SDK 偵測影像中的臉部
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將使用 Azure Face SDK 搭配 C# 來偵測影像中的臉部。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: pafarley
ms.openlocfilehash: 4cad8a01ac70e1f75603d275c621b53cf5595577
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860655"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-net-sdk"></a>快速入門：使用 Face .NET SDK 偵測影像中的臉部

在本快速入門中，您將使用 Face 服務 SDK 搭配 C# 來偵測影像中的臉部。 如需本快速入門中程式碼的實作範例，請在 GitHub 上參閱[認知服務視覺 csharp 快速入門](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/Face)存放庫中的 Face 專案。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。 

## <a name="prerequisites"></a>必要條件

- 臉部 API 訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=face-api)取得免費的試用訂用帳戶金鑰。 或是，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱臉部 API 服務並取得金鑰。
- 任何 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 版本。

## <a name="create-the-visual-studio-project"></a>建立 Visual Studio 專案

1. 在 Visual Studio 中建立新的**主控台應用程式 (.NET Framework)** 專案，並將其命名為 **FaceDetection**。 
1. 如果您的解決方案中有其他專案，請選取此專案作為單一啟始專案。
1. 取得必要的 NuGet 套件。 以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [管理 NuGet 套件]。 按一下 [瀏覽] 索引標籤，並選取 [包含發行前版本]；然後尋找並安裝下列套件：
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-face-detection-code"></a>新增臉部偵測程式碼

開啟新專案的 Program.cs 檔案。 在這裡，您將新增載入影像及偵測臉部所需的程式碼。

### <a name="include-namespaces"></a>包含命名空間

在 *Program.cs* 檔案的頂端新增下列 `using` 陳述式。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=1-7)]

### <a name="add-essential-fields"></a>新增必要欄位

將下列欄位新增到 **Program** 類別。 這項資料會指定連線到 Face 服務的方式，以及接收輸入資料的位置。 您將需要以訂用帳戶金鑰更新 `subscriptionKey` 欄位的值，而且可能需要變更 `faceEndpoint` 字串，使其包含正確的區域識別碼。 您也需要為指向實際影像檔的路徑設定 `localImagePath` 及/或 `remoteImageUrl` 值。

`faceAttributes` 欄位只是特定屬性類型的陣列。 其用於指定要對偵測到的臉部擷取哪些資訊。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=13-34)]

### <a name="create-and-use-the-face-client"></a>建立和使用 Face 用戶端

接下來，對 **Program** 類別的 **Main** 方法新增下列程式碼。 這會設定臉部 API 用戶端。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=38-41)]

此外，在 **Main** 方法中新增下列程式碼，即可使用新建的 Face 用戶端來偵測遠端影像和本機影像中的臉部。 接下來將會定義偵測方法。 

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=43-49)]

### <a name="detect-faces"></a>偵測臉部

將下列方法新增至 **Program** 類別。 這將會使用 Face 服務用戶端偵測遠端影像中的臉部 (以 URL 進行參照)。 請注意，此方法會使用 `faceAttributes` 欄位 &mdash; 新增至 `faceList` 的 **DetectedFace** 物件將具有指定屬性 (在此案例中是年齡和性別)。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=52-74)]

同樣地，新增 **DetectLocalAsync** 方法。 這將會使用 Face 服務用戶端偵測本機影像中的臉部 (以檔案路徑進行參照)。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=76-101)]

### <a name="retrieve-and-display-face-attributes"></a>擷取並顯示臉部屬性

接下來，定義 **GetFaceAttributes** 方法。 此方法會傳回具有相關屬性資訊的字串。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=103-116)]

最後，定義 **DisplayAttributes** 方法，將臉部屬性資料寫入主控台輸出中。

[!code-csharp[](~/cognitive-services-vision-csharp-sdk-quickstarts/Face/Program.cs?range=118-123)]

## <a name="run-the-app"></a>執行應用程式

成功的回應會顯示影像中每張臉的性別與年齡。 例如︰

```
https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg
Male 37   Female 56
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已建立簡單的 .NET 主控台應用程式，可以使用臉部 API 服務來偵測本機影像和遠端影像中的人臉。 接下來，請遵循更深入的教學課程，了解如何以直覺方式將臉部資訊呈現給使用者。

> [!div class="nextstepaction"]
> [教學課程：建立 WPF 應用程式來偵測並影像中的臉部](../Tutorials/FaceAPIinCSharpTutorial.md)
