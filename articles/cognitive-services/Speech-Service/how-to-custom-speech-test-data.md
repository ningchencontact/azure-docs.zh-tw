---
title: 準備測試資料的自訂語音-語音服務
titlesuffix: Azure Cognitive Services
description: 您要測試以查看正確度如何 Microsoft 語音辨識或訓練您自己的模型，您將需要的資料 （音訊和/或文字的形式）。 在此頁面上，我們將討論資料、 使用方式以及如何管理它們的類型。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: de2f1009c574d9768330d4e6a38a219ba1f81daa
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237945"
---
# <a name="prepare-data-for-custom-speech"></a>準備資料以自訂語音

您要測試以查看正確度如何 Microsoft 語音辨識或訓練您自己的模型，您將需要的音訊和文字格式的資料。 在此頁面上，我們將討論資料、 使用方式以及如何管理它們的類型。

## <a name="data-types"></a>資料類型

下表列出可接受的資料類型時應該使用每種資料類型及建議的數量。 並非每個資料類型，才能建立模型。 資料需求會根據您是在建立測試或定型模型而有所不同。

| 数据类型 | 使用的測試 | 數量 | 用於定型 | 數量 |
|-----------|-----------------|----------|-------------------|----------|
| [音訊](#audio-data-for-testing) | 有<br>使用進行視覺檢查 | 5 + 音訊檔案 | 無 | N/a |
| [音訊 + 人類看得標示的文字記錄](#audio--human-labeled-transcript-data-for-testingtraining) | 有<br>用來評估精確度 | 0.5-5 小時的音訊 | 有 | 1-1,000 小時音訊 |
| [相關的文字方塊](##related-text-data-for-training) | 無 | N/a | 有 | 1-200 MB 的相關的文字 |

檔案應該依類型至資料集，和上傳為 zip 檔案。 每個資料集只能包含單一資料類型。

## <a name="upload-data"></a>上傳資料

當您準備要上傳您的資料時，請按一下**將資料上傳**啟動精靈，並建立您的第一個資料集。 系統會要求您選取的語音資料類型，您的資料集，然後才允許您上傳您的資料。

![從語音入口網站中選取音訊](./media/custom-speech/custom-speech-select-audio.png)

您上傳每個資料集必須符合您所選擇的資料類型的需求。 請務必正確地格式化您的資料，才能上傳。 這可確保自訂語音服務將會正確處理資料。 下列各節列出的需求。

上傳您的資料集之後，您會有幾個選項：

* 您可以瀏覽**測試**索引標籤，並以視覺化方式檢查 僅限音訊 + 人類看得標示為轉譯資料。
* 您可以瀏覽**訓練** 索引標籤和音訊 + 人力的轉譯資料或相關的文字資料訓練自訂模型。

## <a name="audio-data-for-testing"></a>音訊資料來進行測試

音訊資料是最佳選擇用於測試 Microsoft 的基準語音轉換文字模型或自訂模型的精確度。 請記住，音訊資料用來檢查特定模型的效能相關的語音的精確度。 如果您想要量化模型的精確度，使用[音訊 + 人類看得標示為轉譯資料](#audio--human-labeled-transcript-data-for-testingtraining)。

您可以使用此表格來確保您的音訊檔案的格式是否正確搭配自訂語音：

| 屬性 | Value |
|----------|-------|
| 檔案格式 | RIFF (WAV) |
| 採樣速率 | 8,000 Hz 或 16,000 Hz |
| 聲道 | 1 (mono) |
| 每個音訊的最大長度 | 2 小時 |
| 樣本格式 | PCM，16 位元 |
| 封存格式 | .zip |
| 封存大小上限 | 2 GB |

如果您音訊不滿足這些屬性，或您想要檢查 如果沒有，我們建議您下載[sox](http://sox.sourceforge.net)檢查或轉換音訊。 以下是如何完成這些活動的每個透過命令列的一些範例：

| activities | 說明 | Sox 命令 |
|----------|-------------|-------------|
| 核取音訊格式 | 您可以使用此命令來檢查音訊檔案格式。 | `sox --i <filename>` |
| 將音訊格式轉換 | 使用此命令將音訊檔案轉換成單一通道，16 位元，16 KHz。 | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>用於測試/定型的音訊 + 人類看得標示的文字記錄資料

若要測量精確度的 Microsoft 的語音轉換文字精確度處理音訊檔案時，您必須提供人類看得標記轉譯 （--逐字） 進行比較。 雖然人類看得標示的文字記錄通常很耗費時間，就必須評估精確度，並針對您的使用案例訓練模型。 請記住，才適合所提供的資料中辨識的改善。 基於這個理由，很重要上, 傳高品質文字記錄。  

| 屬性 | Value |
|----------|-------|
| 檔案格式 | RIFF (WAV) |
| 採樣速率 | 8,000 Hz 或 16,000 Hz |
| 聲道 | 1 (mono) |
| 每個音訊的最大長度 | 60 s |
| 樣本格式 | PCM，16 位元 |
| 封存格式 | .zip |
| 最大的壓縮大小 | 2 GB |

像是 word 刪除或替換、 大量資料，才能改善辨識，就會解決問題。 一般而言，建議您提供大約 10 到 1,000 小時的音訊 word 的 word 轉譯。 所有 WAV 檔案的文字記錄應包含在單一純文字檔案中。 文字記錄檔案的每一行都應包含其中一個音訊檔案的名稱，然後後面接著相對應的文字記錄。 檔案名稱和文字記錄應該以定位字元 (\t) 分隔。

  例如：
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> 文字記錄應使用 UTF-8 位元組順序標記 (BOM) 編碼。

文字記錄會經過文字正規化，以便系統進行處理。 不過，有一些重要的正規化，必須由使用者在將資料上傳到自訂語音服務_之前_完成。 若要準備您的轉譯時，使用適當的語言，請參閱[如何建立人類看得標示的文字記錄](how-to-custom-speech-human-labeled-transcriptions.md)

已經全部收齊您的音訊檔和對應轉譯之後，它們應該封裝成單一.zip 檔案上傳至自訂語音入口網站之前。 這是具有三個音訊檔和人類看得標示的文字記錄檔的範例資料集：

![從語音入口網站中選取音訊](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>相關的文字資料進行訓練

如果您有產品名稱或功能，是唯一的而且您想要確定正確地辨識，務必包含相關的文字資料進行訓練。 兩種類型的相關的文字資料可供改善辨識：

| 数据类型 | 這項資料可辨識的改善 |
|-----------|------------------------------------|
| 談話和/或句子 | 這些可以辨認的產品名稱或業界特定詞彙，句子的內容中時，改善精確度。 |
| 發音 | 這些可以改善未定義的發音與不常見的詞彙、 首字母縮略字或其他文字的發音。 |

談話可以當做單一或多個文字檔案。 越接近資料是什麼要讀出的文字，越精確度已獲得改善。 為單一的文字檔案。 應該提供發音。 可以為單一 zip 檔案封裝並上傳到自訂語音入口網站的所有項目。

### <a name="guidelines-to-create-an-utterances-file"></a>若要建立的談話檔案的指導方針

若要建立自訂模型使用相關的文字方塊，您必須提供一份範例談話。 這些談話不需要完整的句子或文法修正，但它們必須精確地反映您預期在生產環境中的語音的輸入。 如果您想加權已經增加，這是因為某些詞彙，您可以新增幾個句子到相關的資料檔案包含這些特定的詞彙。

您可以使用此表格來確保您的表達方式的相關的資料檔案的格式正確：

| 屬性 | Value |
|----------|-------|
| 文字編碼 | UTF-8 BOM |
| 每一行的語句數目 | 1 |
| 檔案大小上限 | 200 MB |

此外，您會想考量下列限制：

* 避免重複四次以上的字元。 例如:"aaaa"或"uuuu 」。
* 請勿使用特殊字元或 utf-8 字元 U + 00A1 上方。
* Uri 將會遭到拒絕。

### <a name="guidelines-to-create-a-pronunciation-file"></a>若要建立的發音檔案的指導方針

如果有不常見的詞彙，而不需要您的使用者將會遇到，或使用標準發音，您可以提供自訂的發音檔案來改善其辨識。

> [!IMPORTANT]
> 不建議使用這項功能，改變之常見單字的發音。

這是每個包括口語的 utterance 和自訂發音的範例：

| 口語形式 | 辨識/顯示表單 |
|--------------|--------------------------|
| three c p o | 3CPO |  
| c n t k | CNTK |
| i triple e | IEEE |

出口語的形式會是語音拼出的順序。它可以包含字母、 字組、 音節或所有的三種組合。

英文 (EN-US) 和德文 (DE-DE) 使用自訂的發音。 下表顯示依語言的支援的字元：

| 語言 | 地區設定 | 人物 |
|----------|--------|------------|
| 英文 | zh-TW | a、 b、 c、 d、 e、 f、 g、 h、 i、 j、 k、 l、 m、 n、 o、 p、 q、 r、 s、 t、 u、 v、 w、 x、 y、 z |
| 德文 | de-DE | ä、 ö，ü，a、 b、 c、 d、 e、 f、 g、 h、 i、 j、 k、 l、 m、 n、 o、 p、 q、 r、 s、 t、 u、 v、 w、 x、 y、 z |

您可以使用此表格來確保您的發音的相關的資料檔案的格式正確。 發音檔案很小，且不應超過幾 Kb。

| 屬性 | Value |
|----------|-------|
| 文字編碼 | Utf-8 BOM （ANSI 也支援適用於英文） |
| # 個每一行的發音 | 1 |
| 檔案大小上限 | 1 MB (免費層的 1 KB) |

## <a name="next-steps"></a>後續步驟

* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [訓練您的模型](how-to-custom-speech-train-model.md)
* [將模型部署](how-to-custom-speech-deploy-model.md)
