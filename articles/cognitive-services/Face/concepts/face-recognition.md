---
title: 臉部辨識概念
titleSuffix: Azure Cognitive Services
description: 了解臉部辨識相關的概念。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890876"
---
# <a name="face-recognition-concepts"></a>臉部辨識概念

這篇文章說明驗證、 尋找相似、 群組和識別臉部辨識作業和基礎資料結構的概念。 廣義來說，辨識會描述比較兩個不同的臉部，以判斷它們是否是類似的或者屬於同一個人的工作。

## <a name="recognition-related-data-structures"></a>辨識相關的資料結構

辨識作業使用主要的下列的資料結構。 這些物件會儲存在雲端，而且可以參考其識別碼字串。 識別碼字串都是唯一的訂用帳戶內。 可以重複名稱欄位。

|名稱|說明|
|:--|:--|
|DetectedFace| 擷取此單一字體表示法[臉部偵測](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)作業。 建立後的 24 小時到期其識別碼。|
|PersistedFace| 當 DetectedFace 物件加入至群組，例如 FaceList 或人員，它們會變成 PersistedFace 物件。 也很[擷取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)在任何時間，並不會過期。|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)或[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| 此資料結構是 PersistedFace 物件的各種的清單。 FaceList 都有唯一的識別碼、 名稱字串，而選擇性使用者資料字串。|
|[人員](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| 此資料結構是屬於同一個人 PersistedFace 物件的清單。 它具有唯一的識別碼、 名稱字串，而選擇性使用者資料字串。|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)或[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| 此資料結構是 Person 物件的各種的清單。 它具有唯一的識別碼、 名稱字串，而選擇性使用者資料字串。 必須是 PersonGroup[定型](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)才能用於辨識作業。|

## <a name="recognition-operations"></a>辨識作業

本節將詳細說明四個辨識作業使用先前所述的資料結構的方式。 每個辨識作業的廣泛說明，請參閱[概觀](../Overview.md)。

### <a name="verify"></a>驗證

[確認](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)作業採用從 DetectedFace 或 PersistedFace 和另一個 face ID 或 Person 物件的臉部識別碼，並判斷它們是否屬於同一個人。 如果您傳入 Person 物件時，您可以選擇性地傳入的 PersonGroup，該使用者屬於哪個來改善效能。

### <a name="find-similar"></a>尋找類似項目

[尋找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)作業需要 face ID 從 DetectedFace 或 PersistedFace 和 FaceList 或其他的臉部識別碼的陣列。 FaceList，它會傳回類似於指定的臉部的人臉小 FaceList。 臉部識別碼的陣列，它同樣地會傳回較小的陣列。

### <a name="group"></a>群組

[群組](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)作業從 DetectedFace 或 PersistedFace 接受各種臉部識別碼的陣列，並傳回相同的識別碼分為數個較小的陣列。 每個 「 群組 」 的陣列包含表面看起來類似的識別碼。 單一 「 messyGroup"陣列包含臉部 Id 的其中任何相似之處找不到。

### <a name="identify"></a>身分識別

[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)作業從 DetectedFace 或 PersistedFace 和 PersonGroup 採用一或多個臉部識別碼，並傳回一份可能屬於每個臉部的 Person 物件。 傳回的 Person 物件會包裝為候選項目物件，其中預測的信心值。

## <a name="input-data"></a>輸入資料

使用下列秘訣，以確保您輸入的映像提供最精確的辨識結果：

* 支援的輸入的影像格式是 JPEG、 PNG、 GIF （第一個畫面格）、 BMP。
* 映像檔大小應該不能大於 4 MB。
* 當您建立 Person 物件時，使用功能的各種不同的角度和光源的相片。
* 某些臉部可能無法辨識技術挑戰，因為這類：
  * 背光極端光源，比方說，嚴重的影像。
  * 封鎖一或兩個眼睛的障礙物。
  * 線的型別或臉部線的差異。
  * 因為年齡的臉部外觀的變更。
  * 極大的臉部表情。

## <a name="next-steps"></a>後續步驟

既然您已熟悉使用臉部辨識概念，了解如何撰寫指令碼可識別對已訓練的 PersonGroup 的臉部。

* [識別影像中的人臉](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)