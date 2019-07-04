---
title: 使用 HeadPose 屬性
titleSuffix: Azure Cognitive Services
description: 了解如何使用 HeadPose 屬性會自動旋轉臉部方框，或在摘要影片中偵測前端的筆勢。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058587"
---
# <a name="use-the-headpose-attribute"></a>使用 HeadPose 屬性

本指南中，您會看到如何使用偵測到的臉部的 HeadPose 屬性來啟用一些重要的案例。

## <a name="rotate-the-face-rectangle"></a>旋轉的臉部方框

臉部方框，隨著每個偵測到的臉部，傳回標記的位置和大小的映像中的面貌。 根據預設，矩形永遠與對齊 （其兩邊都是垂直和水平） 的影像。這可以是效率不佳的框架有角度的表面。 在您要以程式設計方式裁剪影像中的臉部的情況下，最好能夠旋轉裁剪矩形。

[認知服務臉部 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)範例應用程式使用 HeadPose 屬性來旋轉其偵測到的臉部方框。

### <a name="explore-the-sample-code"></a>探討範例程式碼

以程式設計的方式，您就可以使用 HeadPose 屬性來旋轉臉部方框。 如果您指定這個屬性，偵測臉時 (請參閱[如何偵測臉部](HowtoDetectFacesinImage.md))，您可以稍後再進行查詢。 下列方法從[認知服務臉部 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)應用程式會採用一份**DetectedFace**物件，並傳回一份 **[臉部](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** 物件。 **臉部**如下存放區臉部資料，包括更新的矩形座標的自訂類別。 新的值會計算**頂端**，**左**，**寬度**，並**高度**，並以新欄位**FaceAngle**指定的旋轉。

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

### <a name="display-the-updated-rectangle"></a>顯示更新的矩形

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

## <a name="detect-head-gestures"></a>偵測到前端的筆勢

您可以偵測等 nodding 前端來追蹤即時的 HeadPose 變更搖晃前端的筆勢。 您可以使用這項功能，為自訂的作用偵測器。

作用偵測是決定主體真人和不影像或視訊表示的工作。 前端的動作偵測器可做為一種方式可確認作用中，特別是而不是個人的映像表示法。

> [!CAUTION]
> 若要即時偵測前端的筆勢，您必須呼叫臉部 API，以較高的速率 （超過每秒一次）。 如果您有免費層 (f0) 的訂用帳戶，這將不可能。 如果您有付費層訂用帳戶，請確定您已計算的成本進行快速的 API 呼叫的標頭軌跡偵測。

請參閱[臉部 API HeadPose 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample)標頭的操作範例的 GitHub 上軌跡偵測。

## <a name="next-steps"></a>後續步驟

請參閱[認知服務臉部 WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)旋轉的臉部方框的實用範例的 GitHub 上的應用程式。 或者，請參閱[臉部 API HeadPose 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples)應用程式，這會追蹤即時偵測前端移動 HeadPose 屬性。