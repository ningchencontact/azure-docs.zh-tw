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
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572544"
---
# <a name="face-detection-and-attributes"></a>臉部偵測和屬性

這篇文章說明臉部偵測和臉部屬性資料的概念。 臉部偵測是尋找映像中的人臉，並選擇性地傳回臉部相關資料的各種不同的動作。

您使用[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)作業來偵測影像中的臉部。 每個偵測到最少臉部將會對應到**faceRectangle**欄位在回應中。 這是一組像素座標 （左側、 頂端、 width、 height） 標記所在的臉部。 您可以使用這些座標，來取得所面臨的以及其大小的位置。 在 API 回應中，臉部詳列於從大到最小的大小順序。

## <a name="face-id"></a>臉部識別碼

Face ID 是只是每個偵測到臉部在影像中的唯一識別項字串。 您可以要求中的臉部識別碼您[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API 呼叫。

## <a name="face-landmarks"></a>臉部特徵點

人脸特征点是人脸上的一组易于查找的点，例如瞳孔或鼻尖。 預設會有 27 個預先定義的臉部特徵點。 下图显示了所有 27 个点：

![與所有標示為的 27 個地標臉部圖表](../Images/landmarks.1.jpg)

以像素為單位，會傳回的點座標。

## <a name="attributes"></a>屬性

屬性是額外的表面功能可以選擇性地偵測到的一組[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)API。 以下是可以偵測到的屬性：

* **年齡**估計的時代，幾年來，特定的正面。
* **模糊**blurriness 映像中的字體。 這會傳回介於零和一以及非正式的評等 （「 低 」、 「 中型 」、 「 高 」）。
* **公開**映像中面臨的風險。 這會傳回介於零和一以及非正式的評等 （「 underExposure"、"goodExposure"、"曝光不足 」）。
* **表情**一份其偵測放心個指定的臉部表情。 置信度分数会进行标准化：所有情感的分数加起来后会得到一个总的分数。 返回的情感包括快乐、悲伤、中性、愤怒、蔑视、厌恶、惊讶、恐惧。
* **鬍子**的估計的鬍子，目前狀態和長度為指定的臉部。
* **性別**估計指定臉部的性別。 可能的值為"male"，"female"，"genderless 」。
* **眼鏡**指定的臉孔是否有眼鏡。 可能的值為"NoGlasses"、"ReadingGlasses"、"太陽眼鏡 」，和 「 Swimming Goggles"。
* **線**線樣式的字體。 這會顯示是否線會顯示、 是否偵測到 baldness，和偵測到哪些線色彩。
* **標頭會造成**在 3D 空間中的臉部的方向。 這被描述字幅、 向前復原，然後繞角度以度數為單位。 值範圍是 [-90，90] [-180，180] 和 [-90，90] 度分別。 請參閱下圖中的角度對應：

    ![音調、 使用的標頭，而且偏航標示為軸](../Images/headpose.1.jpg)
* **結構**臉部是否具有結構。 這會傳回"eyeMakeup 」 和 「 lipMakeup"的布林值。
* **雜訊**視覺上偵測到臉部映像中的干擾。 這會傳回介於零和一以及非正式的評等 （「 低 」、 「 中型 」、 「 高 」）。
* **阻擋**是否有封鎖的字體的組件的物件。 這會傳回 「 eyeOccluded"、"foreheadOccluded 」 和 「 mouthOccluded"的布林值。
* **微笑**指定臉部的笑臉運算式。 這是零 （沒有笑臉） 與其中一個 （清除笑臉） 之間的值。

> [!IMPORTANT]
> 臉部屬性預測使用統計演算法，並可能未必正確。 根据特性数据做出决策时请小心。

## <a name="input-data"></a>輸入資料

使用下列秘訣，以確保您輸入的映像提供最精確的偵測結果：

* 支援的輸入的影像格式是 JPEG、 PNG、 GIF （第一個畫面格）、 BMP。
* 映像檔大小應該不能大於 4 MB。
* 可偵測的臉部大小範圍是 36x36 到 4096x4096 像素。 此範圍之外的臉部將偵測不到。
* 某些臉部可能無法偵測到由於技術挑戰。 極大的臉部角度 （前端姿勢） 或臉部的阻擋物 （例如太陽眼鏡或封鎖的字體的組件的實際操作的物件），可能會影響偵測。 正面和接近正面的臉部提供最佳的結果。

## <a name="next-steps"></a>後續步驟

既然您已熟悉臉部偵測的概念，了解如何撰寫簡單的指令碼，在特定的映像中偵測到臉部。

* [如何偵測影像中的臉孔](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)