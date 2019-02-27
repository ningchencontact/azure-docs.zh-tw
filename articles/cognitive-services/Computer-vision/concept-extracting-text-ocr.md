---
title: 使用光學字元辨識 (OCR) 擷取文字 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 以光學字元辨識 (OCR) 擷取文字的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310435"
---
# <a name="extract-text-with-optical-character-recognition"></a>使用光學字元辨識擷取文字

電腦視覺的光學字元辨識 (OCR) 功能可偵測影像中的文字內容，並將識別出來的文字詞轉換為電腦可讀取的字元資料流。 您可以將該結果用於搜尋、醫療記錄、保全及銀行業務等多種用途。 

OCR 支援 25 種語言：阿拉伯文、簡體中文、繁體中文、捷克文、丹麥文、荷蘭文、英文、芬蘭文、法文、德文、希臘文、匈牙利文、義大利文、日文、韓文、挪威文、波蘭文、葡萄牙文、羅馬尼亞文、俄文、塞爾維亞文 (斯拉夫文和拉丁文)、斯洛伐克文、西班牙文、瑞典文及土耳其文。 OCR 會自動偵測已偵測文字的語言。

若有必要，OCR 會以角度為單位傳回以水平影像座標軸為依據的旋轉位移，來修正已辨識文字的旋轉。 OCR 也會提供每個文字的框架座標，如下圖所示。

![描畫旋轉影像及其所讀取和描繪文字的圖表](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>影像需求

電腦視覺可以使用 OCR 從符合下列需求的影像擷取文字：

* 必須以 JPEG、PNG、GIF 或 BMP 格式呈現的影像
* 輸入影像的大小必須介於 50 x 50 與 4200 x 4200 像素之間
* 影像中的文字能以 90 度的任何倍數進行旋轉，並可輔以不超過 40 度的小角度旋轉。

## <a name="improving-ocr-accuracy"></a>改善 OCR 正確性

文字辨識的準確性取決於影像品質。 下列情況可能會導致不正確的讀取：

* 影像模糊。
* 手寫或草寫的文字。
* 藝術字型樣式。
* 文字太小。
* 複雜的背景、陰影、文字反光或透視失真。
* 文字開頭處的大寫字母過大或遺漏
* 文字加上了下標、上標或刪除線。

### <a name="ocr-limitations"></a>OCR 限制

在主要由文字所構成的影像上，部分辨識的文字可能會造成誤判。 在某些影像 (特別是沒有任何文字的相片) 上，精確度可能會隨著影像的類型而有極大差異。

## <a name="next-steps"></a>後續步驟

請參閱 [OCR 參考文件](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) \(英文\) 以深入了解。
