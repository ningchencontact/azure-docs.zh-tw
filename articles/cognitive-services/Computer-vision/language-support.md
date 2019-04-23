---
title: 語言支援-電腦視覺
titleSuffix: Azure Cognitive Services
description: 電腦視覺功能所支援的自然語言的清單。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012665"
---
# <a name="language-support-for-computer-vision"></a>Computer Vision 的語言支援

Computer Vision 的某些功能支援多種語言;這裡未提及的任何功能僅支援英文。

## <a name="text-recognition"></a>文字辨識

電腦視覺，可以識別許多語言的文字。 具體而言， [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API 支援各種不同的語言，而[讀取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)API 並[辨識文字](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)API 僅支援英文。 請參閱[辨識列印和手寫文字](concept-recognizing-text.md)如需有關這項功能，每個 API 的優點。

OCR 會自動偵測的語言輸入的資料，這樣就不需要 API 呼叫中指定的語言代碼。 不過，語言代碼永遠會傳回的值為`"language"`JSON 回應中的節點。

|語言| 語言代碼 | OCR API |
|:-----|:----:|:-----:|
|阿拉伯文 | `ar`|✔ |
|中文 (簡體) | `zh-Hans`|✔ |
|中文 (繁體) | `zh-Hant`|✔ |
|捷克文 | `cs` |✔ |
|丹麥文 | `da` |✔ |
|荷蘭文 | `nl` |✔ |
|English | `en` |✔ |
|芬蘭文 | `fi` |✔ |
|法文 | `fr` |✔ |
|德文 | `de` |✔ |
|希臘文 | `el` |✔ |
|匈牙利文 | `hu` |✔ |
|義大利文 | `it` |✔ |
|日文 | `ja` |✔ |
|韓文 | `ko` |✔ |
|挪威文 | `nb` |✔ |
|波蘭文 | `pl` |✔ |
|葡萄牙文 | `pt` |✔ |
|羅馬尼亞文 | `ro` |✔ |
|俄文 | `ru` |✔ |
|塞爾維亞文 (斯拉夫) | `sr-Cyrl` |✔ |
|塞爾維亞文 (拉丁) | `sr-Latn` |✔ |
|斯洛伐克文 | `sk` |✔ |
|西班牙文 | `es` |✔ |
|瑞典文 | `sw` |✔ |
|土耳其文 | `tr` |✔ |

## <a name="image-analysis"></a>影像分析

某些動作[分析-映像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 可能會傳回結果，在其他語言中，以指定`language`查詢參數。 其他動作以英文傳回的結果，無論指定何種語言，與其他人會擲回的例外狀況不支援的語言。 使用指定的動作`visualFeatures`並`details`查詢參數，請參閱[概觀](home.md)取得一份您可以使用影像分析執行的所有動作。

|語言 | 語言代碼 | Categories | 標記 | 描述 | 成人 | 品牌 | 色彩 | 臉部 | ImageType | 物件 | 名人 | 特徵點 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|中文 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|English | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日文 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|葡萄牙文 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|西班牙文 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>後續步驟

開始使用本指南中所述的電腦視覺功能。

* [分析本機映像 (REST)](./quickstarts/csharp-analyze.md)
* [擷取列印的文字 (REST)](./quickstarts/csharp-print-text.md)