---
title: 使用 HeadPose 調整的臉部方框
titleSuffix: Azure Cognitive Services
description: 了解如何使用 HeadPose 屬性會自動旋轉臉部方框。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: ddc5bc522c0d3ac258581f2a48a5c3b755302f01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576523"
---
# <a name="use-the-headpose-attribute-to-adjust-the-face-rectangle"></a>若要調整的臉部方框使用 HeadPose 屬性

本指南中，您將使用偵測到的臉部屬性，HeadPose，若要旋轉的臉部物件的矩形。 程式碼範例在此指南中，從[認知服務臉部 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)範例應用程式，使用.NET SDK。

臉部方框，隨著每個偵測到的臉部，傳回標記的位置和大小的映像中的面貌。 根據預設，矩形永遠與對齊 （其兩邊都是完全垂直和水平） 的影像。這可以是效率不佳的框架有角度的表面。 在您要以程式設計方式裁剪影像中的臉部的情況下，很有幫助，若要能夠旋轉裁剪矩形。

## <a name="explore-the-sample-code"></a>探討範例程式碼

以程式設計的方式，您就可以使用 HeadPose 屬性來旋轉臉部方框。 如果您指定這個屬性，偵測臉時 (請參閱[如何偵測臉部](HowtoDetectFacesinImage.md))，您可以稍後再進行查詢。 下列方法從[認知服務臉部 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)應用程式會採用一份**DetectedFace**物件，並傳回一份**[臉部](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** 物件。 **臉部**如下存放區臉部資料，包括更新的矩形座標的自訂類別。 新的值會計算**頂端**，**左**，**寬度**，並**高度**，並以新欄位**FaceAngle**指定的旋轉。

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

## <a name="display-the-updated-rectangle"></a>顯示更新的矩形

從這裡開始，您可以使用傳回**臉部**顯示器中的物件。 中的下列程式行會[FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml)顯示新的矩形從這項資料的呈現方式：

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="next-steps"></a>後續步驟

請參閱[認知服務臉部 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)旋轉的臉部方框的實用範例的 GitHub 上的應用程式。 或者，請參閱[臉部 API HeadPose 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples)追蹤 HeadPose 屬性，以偵測各種前端移動 nodding （搖晃） 的即時應用程式。