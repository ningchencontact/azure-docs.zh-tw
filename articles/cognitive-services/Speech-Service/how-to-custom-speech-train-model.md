---
title: 自訂語音-語音服務的定型模型
titlesuffix: Azure Cognitive Services
description: 訓練文字-語音是為了增進辨識準確度，Microsoft 的基準模型，或您打算建立的自訂模型就行了。 訓練模型，則使用人類看得標示為轉譯和相關的文字。 先前上傳的音訊資料，以及這些資料集用來改善及定型來辨識單字、 片語、 首字母縮略字、 名稱和其他產品相關詞彙語音轉換文字模型。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: e12cef052db6aabad94b47283eda11f60f3b2b13
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063925"
---
# <a name="train-a-model-for-custom-speech"></a>自訂語音的定型模型

訓練文字-語音是為了增進辨識準確度，Microsoft 的基準模型，或您打算建立的自訂模型就行了。 訓練模型，則使用人類看得標示為轉譯和相關的文字。 先前上傳的音訊資料，以及這些資料集用來改善及定型來辨識單字、 片語、 首字母縮略字、 名稱和其他產品相關詞彙語音轉換文字模型。 多網域中集 （使用者會說，和您預期辨識相關的資料），提供更精確會為您的模型，請中改善的辨識結果。 請記住，藉由將不相關的資料饋送到您的訓練課程中，您可以減少或降低模型的精確度。

## <a name="use-training-to-resolve-accuracy-issues"></a>利用訓練來解決精確度的問題

如果您遇到辨識問題與您的模型，使用人類看得標籤文字記錄和其他訓練的相關的資料可協助以改善精確度。 您可以使用此表格來判斷要用來解決您問題的資料集：

| 使用案例 | 数据类型 | 資料品質 |
|----------|-----------|---------------|
| 會將適當的名稱 | 相關的文字方塊 （句子/談話） | 為 500 MB 的 10 MB |
| 文字會將因為輔色 | 相關的文字方塊 （發音） | 提供 misrecognized 的單字 |
| 刪除或將常見的字詞 | 音訊 + 人類看得標示的文字記錄 | 10 到 1,000 轉譯時數 |

> [!IMPORTANT]
> 如果您還沒有上傳資料集，請參閱[準備和測試您的資料](how-to-custom-speech-test-data.md)。 本文件提供上傳資料，以及建立高品質資料集的指導方針的指示。

## <a name="train-and-evaluate-a-model"></a>訓練及評估模型

定型模型的第一個步驟是將定型資料上傳。 使用[準備和測試您的資料](how-to-custom-speech-test-data.md)如需逐步指示來準備人類看得標示為轉譯和相關的文字方塊 （發音與發音）。 您已上傳定型資料之後，請遵循下列指示來啟動 定型模型：

1. 瀏覽至**語音轉換文字 > 客製化語音 > 訓練**。
2. 按一下 **定型模型**。
3. 接下來，讓您的訓練**名稱**並**描述**。
4. 從**案例和基準模型**下拉式選單中，選取最適合您的網域的案例。 如果您確定要選擇哪個案例，請選取**一般**。 基準模型是訓練的起點。 如果您沒有喜好設定，您可以使用最新版本。
5. 從**選取定型資料**頁面上，選擇您想要用於訓練的一或多個音訊 + 人類看得標示為轉譯資料集。
6. 訓練完成之後，您可以選擇執行測試新定型的模型的精確度。 此為選用步驟。
7. 選取 **建立**來建置自訂模組。

定型資料表會顯示新的項目對應到這個新建立的模型。 資料表也會顯示狀態：處理、 成功、 失敗。

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>評估定型模型的精確度

您可以檢查資料，並評估模型的精確度，使用這些文件：

* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)


如果您選擇測試精確度，務必選取不同於您使用與您的模型來實際了解模型的效能的原音資料集。

## <a name="next-steps"></a>後續步驟

* [將模型部署](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

* [準備和測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [訓練您的模型](how-to-custom-speech-train-model.md)
