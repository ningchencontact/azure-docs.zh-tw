---
title: 辨識列印/手寫文字，電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 辨識影像中印刷和手寫文字的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bcaa990cc2186a5f1eecdbbca91804c92370277c
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357190"
---
# <a name="recognize-printed-and-handwritten-text"></a>辨識印刷和手寫文字

電腦視覺提供許多服務，偵測及擷取列印或手寫影像中顯示的文字。 這是適用於各種不同的案例，例如所記錄的筆記、 醫療記錄、 安全性和銀行業務。 下列三個區段詳細三個不同的文字辨識 Api，每個適合不同使用案例。

## <a name="read-api"></a>讀取 API

讀取 API 會偵測映像使用我們最新的辨識模型中的文字內容，並將辨識出的文字轉換成電腦可讀取的字元資料流。 它被最佳化大量文字的映像 （例如數位已掃描的文件），以及視覺干擾也有許多的映像。 它會判斷哪個辨識来用於模型的每一行文字，支援列印和手寫文字的影像。 讀取 API 以非同步方式執行較大的文件可能需要幾分鐘才能傳回結果。

「 讀取 」 操作會維護原始列群組的辨識字在其輸出中。 週框方塊座標隨附的每一行，每個字的行內也有它自己的座標。 如果某個字已辨識有低的信心，也傳達這項資訊。 請參閱[閱讀 API 參考文件](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)若要深入了。

> [!NOTE]
> 這項功能只適用於英文文字。

### <a name="image-requirements"></a>影像需求

讀取 API 的運作方式與符合下列需求的映像：

- 映像必須是 JPEG、 PNG、 BMP、 PDF 或 TIFF 格式顯示。
- 影像的維度必須介於 50 x 50 和 4200 x 4200 像素為單位。 PDF 頁必須 17 x 17 英吋或較小的大小。
- 映像的檔案大小必須小於 20 mb (MB)。

### <a name="limitations"></a>限制

如果您使用免費層訂用帳戶，閱讀 API 只會處理的 PDF 或 TIFF 文件的前兩個頁面。 與付費的訂用帳戶，它會處理最多 200 個的頁面。 也請注意 API 將會偵測最多 300 每頁行數。

## <a name="ocr-optical-character-recognition-api"></a>OCR （光學字元辨識） API

電腦視覺光學字元辨識 (OCR) API 類似於讀取 API，但它會以同步方式執行並不適合大型文件。 它會使用更多的語言; 較早的辨識模型，但運作方式請參閱[語言支援](language-support.md#text-recognition)如需完整的支援的語言清單。

若有必要，OCR 會以角度為單位傳回以水平影像座標軸為依據的旋轉位移，來修正已辨識文字的旋轉。 OCR 也提供每個字的框架座標，如下圖所示。

![旋轉的映像和它所讀取與分隔的文字](./Images/vision-overview-ocr.png)

請參閱[OCR 參考文件](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)若要深入了。

### <a name="image-requirements"></a>影像需求

OCR API 的運作方式符合下列需求的映像：

* 映像必須是 JPEG、 PNG、 GIF 或 BMP 格式顯示。
* 輸入影像的大小必須介於 50 x 50 到 4200 x 4200 像素為單位。
* 影像中的文字能以 90 度的任何倍數進行旋轉，並可輔以不超過 40 度的小角度旋轉。

### <a name="limitations"></a>限制

在主要由文字構成的相片上，部分辨識的文字可能會造成誤判。 部分相片，尤其是沒有任何文字，相片上有效位數而異的影像類型。

## <a name="recognize-text-api"></a>辨識文字 API

> [!NOTE]
> 辨識文字 API 已由讀取 API 被取代。 讀取 API 具有類似的功能，並會更新，以處理 PDF、 TIFF 和多頁的檔案。

辨識文字 API 類似於 OCR，但它以非同步方式執行，並使用更新的辨識模型。 請參閱[辨識文字 API 參考文件](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)若要深入了。

### <a name="image-requirements"></a>影像需求

辨識文字 API 的運作方式與符合下列需求的映像：

- 映像必須是 JPEG、 PNG 或 BMP 格式顯示。
- 影像的維度必須介於 50 x 50 和 4200 x 4200 像素為單位。
- 映像的檔案大小必須小於 4 mb (MB)。

## <a name="limitations"></a>限制

文字辨識作業的精確度取決於影像的品質。 下列因素可能會導致不正確的讀取：

* 影像模糊。
* 手寫或草寫的文字。
* 藝術字型樣式。
* 文字太小。
* 複雜的背景、陰影、文字反光或透視失真。
* 過大或遺漏大寫字在單字開頭。
* 文字加上了下標、上標或刪除線。

## <a name="next-steps"></a>後續步驟

請遵循[擷取列印的文字 (OCR)](./quickstarts/csharp-print-text.md)快速入門到實作簡單文字辨識C#應用程式。
