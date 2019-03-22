---
title: 检测图像中的人脸 - 人脸 API
titleSuffix: Azure Cognitive Services
description: 了解如何使用人脸检测功能返回的各种数据。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588766"
---
# <a name="get-face-detection-data"></a>获取人脸检测数据

本指南演示如何使用人脸检测功能从给定的图像中提取性别、年龄或姿势等特性。 本指南中的代码片段是使用人脸 API 客户端库以 C# 语言编写的，但可通过 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 使用相同的功能。

本指南将介绍以下操作：

- 获取图像中人脸的位置和维度。
- 获取图像中各个人脸特征点（瞳孔、鼻子、嘴巴等）的位置。
- 猜测性别、年龄和情绪，以及检测到的人脸的其他特性。

## <a name="setup"></a>設定

本指南假设你已使用人脸订阅密钥和终结点 URL 构造了名为 `faceClient` 的 **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 对象。 在此处，可以通过调用 **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)**（本指南中使用）或 **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)** 来使用人脸检测功能。 有关设置方法的说明，请参阅 [C# 人脸检测快速入门](../quickstarts/csharp-detect-sdk.md)。

本指南重点介绍有关检测调用的具体信息 &mdash; 可以传递哪些参数，以及可对返回的数据执行哪些操作。 我们建议仅查询所需的特征，因为每个操作需要花费额外的时间才能完成。

## <a name="get-basic-face-data"></a>获取基本人脸数据

若要查找人脸并获取其在图像中的位置，请在将 _returnFaceId_ 参数设置为 **true**（默认值）的情况下调用该方法。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

可在返回的 **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** 对象中查询其唯一 ID，矩形提供了人脸的像素坐标。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

有关如何分析人脸位置和维度的信息，请参阅 **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)**。 通常情况下，此矩形包含眼睛、眉毛、鼻子和嘴巴，不一定包括头顶、耳朵和下巴。 若要使用人脸矩形来裁剪整个头部或中景肖像（身份证类型的图像），可在特定的边缘朝每个方向拉伸矩形。

## <a name="get-face-landmarks"></a>获取人脸特征点

人脸特征点是人脸上的一组易于查找的点，例如瞳孔或鼻尖。 可以通过将 _returnFaceLandmarks_ 参数设置为 **true** 来获取人脸特征点数据。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

預設會有 27 個預先定義的臉部特徵點。 下图显示了所有 27 个点：

![标有所有 27 个特征点的人脸插图](../Images/landmarks.1.jpg)

返回的点以像素为单位，就像人脸矩形框一样。 以下代码演示如何检索鼻子和瞳孔的位置：

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

人脸特征点数据还可用于准确计算人脸的方向。 例如，可以将人脸的旋转角定义为从嘴巴中心到眼睛中心的矢量。 以下代码计算此矢量：

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

知道人脸的方向后，可以旋转矩形人脸框，使人脸更适当地对齐。 若要裁剪图像中的人脸，可以编程方式旋转图像，使人脸始终朝上。

## <a name="get-face-attributes"></a>获取人脸特性

除了人脸矩形和特征点以外，人脸检测 API 还可以分析人脸的几个概念特性。 其中包含：

- 年齡
- 性別
- 笑容程度
- 臉部毛髮
- 眼镜
- 3D 头部姿势
- 表情 API

> [!IMPORTANT]
> 这些属性是使用统计算法预测的，不一定准确。 根据特性数据做出决策时请小心。
>

若要分析人脸特性，请将 _returnFaceAttributes_ 参数设置为 **[FaceAttributeType 枚举](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 值的列表。

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

然后，获取对返回的数据的引用，并根据需要执行进一步的操作。

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

若要详细了解每个特性，请参阅[词汇表](../Glossary.md)。

## <a name="next-steps"></a>後續步驟

本指南介绍了如何使用人脸检测的各项功能。 接下来请参阅[词汇表](../Glossary.md)，以更详细地了解检索到的人脸数据。

## <a name="related-topics"></a>相關主題

- [参考文档 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [參考文件 (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
