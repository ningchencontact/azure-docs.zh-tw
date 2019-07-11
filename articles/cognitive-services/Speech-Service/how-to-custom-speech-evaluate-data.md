---
title: 評估精確度的自訂語音-語音服務
titlesuffix: Azure Cognitive Services
description: 在這份文件中，您將了解如何以量化方式測量 Microsoft 的語音轉換文字模型或您的自訂模型的品質。 音訊 + 人類看得標示為轉譯資料，才能測試精確度，並應提供具代表性的音訊的 5 個小時 30 分鐘的時間。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 2e9818fad9a0b5d04cc50a293b16d838c319dd86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606577"
---
# <a name="evaluate-custom-speech-accuracy"></a>評估自訂語音準確度

在本文件中，您將學習如何以量化方式測量 Microsoft 的語音轉換文字模型或您的自訂模型的品質。 音訊 + 人類看得標示為轉譯資料，才能測試精確度，並應提供具代表性的音訊的 5 個小時 30 分鐘的時間。

## <a name="what-is-word-error-rate-wer"></a>Word 錯誤速率 (WER) 是什麼？

是業界標準來測量模型精確度*Word 錯誤率*(WER)。 WER 計算期間辨識，識別不正確的字的數目，然後除以總數提供人類看得標示的文字記錄中的文字。 最後，該數字會乘以 100%，以計算 WER。

![WER 公式](./media/custom-speech/custom-speech-wer-formula.png)

無法正確地辨識單字分為三個類別：

* 插入 (I):不正確地加入假設文字記錄中的文字
* 刪除 (D):未偵測到假設文字記錄中的文字
* 替代 (S):參考和假設之間已取代的文字

以下為範例：

![識別不正確的字組的範例](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>解決錯誤，並改善 WER

您可以使用從機器辨識結果 WER 來評估的模型使用您的應用程式、 工具或產品的品質。 WER 的 5%-10%會被視為高品質，並可供使用。 不過，您可能要考慮其他的訓練，是可以接受的 20%的 WER。 WER 30%以上的訊號品質不佳，而且需要自訂與訓練。

錯誤的散發方式相當重要。 當發生許多刪除錯誤時，通常是因為只有微弱的音訊訊號強度。 若要解決此問題，您必須收集更接近音訊資料來源。 插入錯誤表示在吵雜的環境中錄製音訊，而且對話可能會出現，造成辨識問題。 已提供為人類看得標示為轉譯或相關的特定領域詞彙足夠範例時，通常發生替代錯誤文字。

藉由分析個別的檔案，您可以判斷何種錯誤存在，以及哪些錯誤是唯一的特定檔案。 在檔案層級的了解問題可協助您為目標的改進。

## <a name="create-a-test"></a>建立測試

如果您想要測試 Microsoft 的語音轉換文字基準模型或您已訓練過的自訂模型的品質，您可以比較兩個模型以並排方式來評估精確度。 比較包含 WER 和辨識的結果。 一般而言，自訂模型會與 Microsoft 的基準模型進行比較。

若要評估模型並排顯示：

1. 瀏覽至**語音轉換文字 > 客製化語音 > 測試**。
2. 按一下 **加入測試**。
3. 選取 **評估精確度**。 為測試的名稱、 描述，然後選取 音訊 + 人類看得標示為轉譯資料集。
4. 選取您想要測試的最多兩個模型。
5. 按一下 [建立]  。

已成功建立您的測試之後，您可以比較的結果並排顯示。

## <a name="side-by-side-comparison"></a>並排顯示比較

測試完成之後，由狀態變更*Succeeded*，您會發現您的測試中包含這兩種模型 WER 數字。 按一下 測試名稱以檢視測試詳細資料頁面上。 此詳細資料頁面會列出您的資料集，指出兩個模型，以及從提交的資料集轉譯的辨識結果中的所有談話。 為了檢查並排顯示比較，您可以切換包括插入、 刪除和替代的各種錯誤類型。 您可以藉由接聽音訊，並比較每個資料行，顯示人類看得標示的文字記錄中的辨識結果和兩個語音轉換文字模型的結果，決定哪一個模型符合您的需求，以及其中另有其他訓練和增強功能是必要的。

## <a name="next-steps"></a>後續步驟

* [訓練您的模型](how-to-custom-speech-train-model.md)
* [將模型部署](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

* [準備和測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
