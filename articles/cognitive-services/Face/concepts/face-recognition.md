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
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "64416046"
---
# <a name="face-recognition-concepts"></a>臉部辨識概念

這篇文章會說明不同的臉部辨識作業 （驗證、 尋找類似，群組識別） 和基礎資料結構的概念。 廣義來說，辨識會描述比較兩個不同的臉部，以判斷它們是否為類似的工作，或屬於同一個人。

## <a name="recognition-related-data-structures"></a>辨識相關的資料結構

辨識作業使用主要的下列的資料結構。 這些物件會儲存在雲端，而且可以參考其識別碼字串。 識別碼字串因此一律內是唯一的訂用帳戶，而可重複的名稱欄位。

|名稱|描述|
|:--|:--|
|**DetectedFace**| 這是所擷取的單一字體表示法[臉部偵測](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)作業。 建立後的 24 小時到期其識別碼。|
|**PersistedFace**| 當**DetectedFace**物件加入至群組 (例如**FaceList**或**人員**)，它們會變成**PersistedFace**物件，可以要[擷取](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c)在任何時間，因此不會過期。|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| 這是各種的份**PersistedFace**物件。 A **FaceList**具有唯一的識別碼、 名稱字串，以及 （選擇性） 使用者資料字串。|
|**[人員](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| 這是一份**PersistedFace**屬於同一個人的物件。 它具有唯一的識別碼、 名稱字串，而選擇性使用者資料字串。|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| 這是各種的份**人員**物件。 它具有唯一的識別碼、 名稱字串，而選擇性使用者資料字串。 A **PersonGroup**必須[定型](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249)才能用於辨識作業。|

## <a name="recognition-operations"></a>辨識作業

本節將詳細說明四個辨識作業使用上述的資料結構的方式。 請參閱[概觀](../Overview.md)的每個辨識作業的廣泛描述。

### <a name="verification"></a>验证

[確認](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)作業所花費的 face ID (**DetectedFace**或**PersistedFace**) 和其中一個其他的 face ID 或**人員**物件和判斷它們是否屬於同一個人。 如果您傳入**Person**，您可以選擇性地傳入**PersonGroup**該**人員**所屬以提升效能。

### <a name="find-similar"></a>尋找類似

[尋找類似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)作業所花費的 face ID (**DetectedFace**或是**PersistedFace**) 以及**FaceList**或其他臉部的陣列識別碼。 具有**FaceList**，它會傳回較小**FaceList**的類似於指定的臉部的臉部。 臉部識別碼的陣列，它同樣地會傳回較小的陣列。

### <a name="grouping"></a>分組

[群組](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)作業所花費的各種臉部識別碼的陣列 (**DetectedFace**或是**PersistedFace**)，並傳回相同的識別碼分為數個較小的陣列。 每個 「 群組 」 的陣列包含臉部識別碼看起來類似，而單一 「 messyGroup"陣列包含臉部 Id 的其中任何相似之處找不到。

### <a name="identification"></a>識別

[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)作業會採用一或多個臉部識別碼 (**DetectedFace**或是**PersistedFace**) 和**PersonGroup**並傳回清單**人員**每張臉部可能所屬的物件。 傳回**Person**物件會包裝為**候選**有預測信賴值的物件。

## <a name="input-data"></a>輸入資料

使用下列秘訣，以確保您輸入的映像提供最精確的辨識結果：

* 支援的輸入的影像格式是 JPEG、 PNG、 GIF （第一個畫面格）、 BMP。
* 映像檔大小應該不能大於 4 MB。
* 建立時**人員**物件，您應該使用功能的各種不同的角度和光源的相片。
* 某些臉部可能無法辨識因為技術挑戰：
  * 已加上 （比方說，嚴重背光） 的極端光源的映像。
  * 封鎖一或兩個眼睛的限制。
  * 線樣式或臉部線的差異。
  * 由於年齡的臉部外觀的變更。
  * 極大的臉部表情。

## <a name="next-steps"></a>後續步驟

既然您已熟悉臉部辨識概念，了解如何撰寫簡單的指令碼可識別對已定型的臉部**PersonGroup**。

* [如何找出影像中的臉](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)