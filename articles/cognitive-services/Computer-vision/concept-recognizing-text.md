---
title: 辨識印刷和手寫文字 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 辨識影像中印刷和手寫文字的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9bb574fcb9782aad41ea0fd276b8addee19caf01
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588970"
---
# <a name="recognize-printed-and-handwritten-text"></a>辨識印刷和手寫文字

计算机视觉提供多个服务用于检测和提取图像中显示的打印文本或手写文本。 在笔录、医疗记录、保安和银行等场景中，这些服务非常有用。 以下三个部分详细介绍了针对不同用例优化的三个不同文本识别 API。

## <a name="read-api"></a>读取 API

读取 API 使用我们最新的识别模型检测图像中的文本内容，并将已识别的文本转换为机器可读的字符流。 该 API 已针对包含大量文本的图像（例如，数码扫描的文档）以及包含大量视觉噪点的图像进行优化。 该 API 以异步方式执行，因为处理较大文档时，可能需要花费好几分钟才能返回结果。

“读取”操作会在其输出中保留已识别字的原始行分组。 每一行附带边框坐标，行中的每个字也有其自身的坐标。 如果某个字的识别置信度较低，该结果中也会反映该信息。 請參閱[閱讀 API 參考文件](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)若要深入了。

> [!NOTE]
> 此功能目前以预览版提供，仅适用于英语文本。

### <a name="image-requirements"></a>影像需求

读取 API 可以处理符合以下要求的图像：

- 图像必须以 JPEG、PNG、BMP、PDF 或 TIFF 格式显示。
- 图像的尺寸必须介于 50 x 50 和 4200 x 4200 像素之间。 PDF 页面必须为 17 x 17 英寸或更小。
- 图像的文件大小必须小于 20 MB。

### <a name="limitations"></a>限制

如果使用的是免费层订阅，读取 API 只会处理 PDF 或 TIFF 文档的前两个页面。 使用付费订阅时，它最多可以处理 200 个页面。 另请注意，该 API 最多检测每个页面中的 300 行。

## <a name="ocr-optical-character-recognition-api"></a>OCR（光学字符识别）API

计算机视觉的光学字符识别 (OCR) API 类似于读取 API，但它以同步方式执行，且未针对大型文档进行优化。 该 API 使用早期的识别模型，但可以处理更多的语言。

OCR 支援 25 種語言：阿拉伯文、簡體中文、繁體中文、捷克文、丹麥文、荷蘭文、英文、芬蘭文、法文、德文、希臘文、匈牙利文、義大利文、日文、韓文、挪威文、波蘭文、葡萄牙文、羅馬尼亞文、俄文、塞爾維亞文 (斯拉夫文和拉丁文)、斯洛伐克文、西班牙文、瑞典文及土耳其文。 OCR 會自動偵測已偵測文字的語言。

若有必要，OCR 會以角度為單位傳回以水平影像座標軸為依據的旋轉位移，來修正已辨識文字的旋轉。 OCR 还提供每个字的帧坐标，如下图所示。

![描畫旋轉影像及其所讀取和描繪文字的圖表](./Images/vision-overview-ocr.png)

有关详细信息，请参阅 [OCR 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)。

### <a name="image-requirements"></a>影像需求

OCR API 可以处理符合以下要求的图像：

* 图像必须以 JPEG、PNG、GIF 或 BMP 格式显示。
* 输入图像的大小必须介于 50 x 50 和 4200 x 4200 像素之间。
* 影像中的文字能以 90 度的任何倍數進行旋轉，並可輔以不超過 40 度的小角度旋轉。

### <a name="limitations"></a>限制

在主要由文字構成的相片上，部分辨識的文字可能會造成誤判。 在某些照片上，尤其是在不包含任何文本的照片上，精度因图像的类型而异。

## <a name="recognize-text-api"></a>识别文本 API

> [!NOTE]
> 随着读取 API 的推出，识别文本 API 已弃用。 读取 API 具有类似的功能，经更新后可以处理 PDF、TIFF 和多页文件。

识别文本 API 类似于 OCR，但它以异步方式执行，并使用更新的识别模型。 有关详细信息，请参阅[识别文本 API 参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)。

### <a name="image-requirements"></a>影像需求

识别文本 API 可以处理符合以下要求的图像：

- 图像必须以 JPEG、PNG 或 BMP 格式显示。
- 图像的尺寸必须介于 50 x 50 和 4200 x 4200 像素之间。
- 图像的文件大小必须小于 4 MB。

## <a name="improve-results"></a>改善结果

文本识别操作的准确度取决于图像的质量。 以下因素可能导致读取结果不准确：

* 影像模糊。
* 手寫或草寫的文字。
* 藝術字型樣式。
* 文字太小。
* 複雜的背景、陰影、文字反光或透視失真。
* 文本过长，或单词的开头缺少大写字母。
* 文字加上了下標、上標或刪除線。

## <a name="next-steps"></a>後續步驟

請遵循[擷取列印的文字 (OCR)](./quickstarts/csharp-print-text.md)快速入門到實作簡單文字辨識C#應用程式。
