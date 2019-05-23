---
title: 臉部偵測和屬性的概念
titleSuffix: Azure Cognitive Services
description: 了解臉部偵測和臉部屬性相關的概念。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891161"
---
# <a name="face-detection-and-attributes"></a>臉部偵測和屬性

這篇文章說明臉部偵測和臉部屬性資料的概念。 臉部偵測是尋找映像中的人臉，並選擇性地傳回臉部相關資料的不同類型的動作。

您使用[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)作業來偵測影像中的臉部。 最小值，每個偵測到的表面對應於 faceRectangle 欄位在回應中。 左側、 上方、 寬度和高度的像素座標這組標記所在的臉部。 您可以使用這些座標，來取得所面臨的和其大小的位置。 在 API 回應中，臉部詳列於從大到最小的大小順序。

## <a name="face-id"></a>臉部識別碼

Face ID 是唯一的識別碼字串中每個偵測到臉部影像。 您可以要求中的臉部識別碼您[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API 呼叫。

## <a name="face-landmarks"></a>臉部特徵點

臉部特徵是一組容易尋找點上的表面，例如 pupils 或鼻子的提示。 預設會有 27 個預先定義的臉部特徵點。 下圖顯示 27 的所有點：

![與所有標示為的 27 個地標臉部圖表](../Images/landmarks.1.jpg)

以像素為單位，會傳回的點座標。

## <a name="attributes"></a>屬性

屬性是一組可以選擇性地偵測到的功能[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API。 可偵測到下列屬性：

* **年齡**。 在多年的特定臉部估計的年齡。
* **模糊**。 映像中的字體 blurriness。 這個屬性會傳回值介於 0 和一到非正式的評等的低、 中或高。
* **表情**。 一份其偵測放心個指定的臉部表情。 信心分數會正規化，並跨所有的情感分數新增至其中。 傳回的表情會快樂、 悲傷、 中性、 生氣、 藐視、 厭惡、 意外、 和恐懼。
* **公開**。 映像中面臨的風險。 這個屬性會傳回值介於 0 和一到 underExposure、 goodExposure 或過度曝光的非正式評等。
* **鬍子**。 估計的鬍子，目前狀態和長度之指定的表面。
* **性別**。 指定的臉部估計的性別。 可能的值為男性、 女性，以及 genderless。
* **眼鏡**。 指定的臉孔是否眼鏡。 可能的值為 NoGlasses、 ReadingGlasses、 太陽眼鏡、 和 Swimming Goggles。
* **線**。 所面臨的線類型。 此屬性會顯示是否線會顯示、 是否偵測到 baldness，和偵測到哪些線色彩。
* **標頭會造成**。 在 3D 空間中的臉部的方向。 這個屬性被描述字幅、 向前復原，然後繞角度以度數為單位。 值範圍可以分別表示介於-90 度到 90 度、-180 度，以 180 度，並介於-90 度到 90 度。 請參閱下圖中的角度對應：

    ![音調、 使用的標頭，而且偏航標示為軸](../Images/headpose.1.jpg)
* **結構**。 是否所面臨的具有結構。 此屬性會傳回 eyeMakeup 和 lipMakeup 的布林值。
* **雜訊**。 視覺化的雜訊，偵測到臉部映像中。 這個屬性會傳回值介於 0 和一到非正式的評等的低、 中或高。
* **阻擋**。 是否有物件封鎖的組件的字體。 此屬性會傳回 eyeOccluded、 foreheadOccluded 和 mouthOccluded 的布林值。
* **微笑**。 指定的臉部笑臉運算式。 此值為零不笑臉，另一個用於清除笑臉之間。

> [!IMPORTANT]
> 臉部屬性預測透過統計演算法使用。 它們不一定準確。 當您進行決策基礎屬性資料時，請務必小心。

## <a name="input-data"></a>輸入資料

使用下列秘訣，請確定您輸入的映像提供最精確的偵測結果：

* 支援的輸入的影像格式會是第一個畫面格，和 BMP JPEG、 PNG、 GIF。
* 映像檔大小應該是不大於 4 MB。
* 可偵測的臉部大小範圍為 36 x 36 至 4096 x 4096 像素。 此範圍之外的臉部將偵測不到。
* 某些臉部可能無法偵測由於技術挑戰。 極大的臉部角度 （前端姿勢） 或臉部的阻擋物 （例如太陽眼鏡或封鎖的字體的組件的實際操作的物件），可能會影響偵測。 正面和接近正面的臉部提供最佳的結果。

如果您要偵測摘要影片中的臉孔，您可以藉由調整視訊攝影機上的特定設定來改善效能：

* **平滑化**:許多攝影機套用平滑效果。 您應該關閉這個如果您可以因為它會建立柔邊畫面格之間，且能減少清晰度。
* **快門速度**:更快速的快門減少畫面格之間的動作，並讓更清楚每個畫面格。 我們建議快門的 1/60 秒或更快的速度。
* **快門角度**:有些攝影機指定快門角度而不是快門速度。 您應該盡可能使用較低的快門角度。 這會導致更清楚的視訊畫面。

    >[!NOTE]
    > 具有較低的快門角度相機會在每個畫面格，收到較少的光線，讓映像將會較暗。 您必須決定要使用的正確層級。

## <a name="next-steps"></a>後續步驟

既然您已熟悉使用臉部偵測的概念，了解如何撰寫指令碼，在特定的映像中偵測到臉部。

* [偵測影像中的臉孔](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)