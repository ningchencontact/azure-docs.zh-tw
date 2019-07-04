---
title: 偵測映像-臉部 API 中的臉孔
titleSuffix: Azure Cognitive Services
description: 了解如何使用各種臉部偵測功能所傳回的資料。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124540"
---
# <a name="get-face-detection-data"></a>取得臉部偵測資料

本指南示範如何從特定的映像擷取的屬性，例如性別、 年齡或姿勢使用臉部偵測。 本指南中的程式碼片段會以C#使用 Azure 認知服務臉部 API 的用戶端程式庫。 相同的功能是透過[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。

本指南將說明如何以：

- 取得映像的位置和維度的臉部。
- 取得各種不同的臉部特徵，例如 pupils、 鼻子及說話，映像中的位置。
- 猜測性別、 年齡、 表情和偵測的臉部的其他屬性。

## <a name="setup"></a>設定

本指南假設已經建構[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)物件，名為`faceClient`，使用臉部訂用帳戶金鑰和端點 URL。 從這裡開始，您可以使用臉部偵測功能，藉由呼叫[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)，這會在本指南中，或是[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)。 如需有關如何設定這項功能的指示，請參閱 <<c0> [ 偵測所面臨的快速入門C# ](../quickstarts/csharp-detect-sdk.md)。</c0>

本指南著重於偵測呼叫的詳細資訊，例如哪些引數，您可以傳遞和用途使用傳回的資料。 我們建議您查詢需要的功能。 每個作業會需要額外的時間才能完成。

## <a name="get-basic-face-data"></a>取得基本臉部資料

要尋找臉部，並取得其映像中的位置，使用呼叫方法_returnFaceId_參數設為 **，則為 true**。 這項設定是預設值。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

您可以查詢所傳回[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)物件的唯一識別碼和矩形，可讓表面的像素座標。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

如需如何剖析的位置和維度所面臨的資訊，請參閱[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)。 通常，這個矩形所包含的眼睛、 眉毛、 鼻子及說話。 上方的標頭、 耳朵，chin 不一定包含項目。 若要使用臉部方框裁剪的完整標頭或映像的相片識別碼類型可用於取得擷取畫面中間直向，您可以展開每個方向的矩形。

## <a name="get-face-landmarks"></a>取得臉部特徵

[面臨地標](../concepts/face-detection.md#face-landmarks)是一組容易尋找點上的表面，例如 pupils 或鼻子的提示。 若要取得臉部地標資料，請設定_returnFaceLandmarks_參數來 **，則為 true**。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

下列程式碼示範如何，您可能會擷取鼻子及 pupils 的位置：

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

您也可以使用臉部地標資料精確計算朝的方向。 例如，您可以定義表面的旋轉以中央的眼睛從中央說話的向量。 下列程式碼會計算這個向量：

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

當您知道朝的方向時，您可以旋轉至更適當地將它對齊的矩形的臉部框架。 若要裁剪影像中的臉部，您以程式設計的方式可以旋轉映像，以使表面永遠顯示垂直。

## <a name="get-face-attributes"></a>取得臉部屬性

除了臉部方框和地標，臉部偵測 API 可以分析臉部的幾個概念的屬性。 如需完整清單，請參閱 <<c0> [ 臉部屬性](../concepts/face-detection.md#attributes)概念的區段。

若要分析的臉部屬性，設定_returnFaceAttributes_參數，以一份[FaceAttributeType 列舉](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)值。

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

然後，取得傳回的資料的參考，並執行更多的作業，根據您的需求。

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

若要深入了解每個屬性，請參閱[臉部偵測和屬性](../concepts/face-detection.md)概念指南。

## <a name="next-steps"></a>後續步驟

在本指南中，您已了解如何使用各種不同的臉部偵測功能。 接下來，這些功能整合至您的應用程式所遵循的深入教學課程。

- [教學課程：建立 WPF 應用程式，顯示映像中的臉部資料](../Tutorials/FaceAPIinCSharpTutorial.md)
- [教學課程：建立 Android 應用程式來偵測及畫面格影像中的人臉](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>相關主題

- [參考文件 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [參考文件 (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)