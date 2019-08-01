---
title: 準備自訂語音語音服務的測試資料
titleSuffix: Azure Cognitive Services
description: 無論您是否正在測試, 以瞭解 Microsoft 語音辨識的正確性或如何訓練您自己的模型, 您將需要資料 (格式為音訊和/或文字)。 在此頁面上, 我們將討論資料類型、使用方式, 以及如何管理它們。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fad0fada4d9dd888b0b2a37b59e4eac1e016aec4
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663589"
---
# <a name="prepare-data-for-custom-speech"></a>準備自訂語音的資料

無論您是否正在測試, 以瞭解 Microsoft 語音辨識的正確性或如何訓練您自己的模型, 您都需要音訊和文字形式的資料。 在此頁面上, 我們將討論資料類型、使用方式, 以及如何管理它們。

## <a name="data-types"></a>資料類型

此資料表會列出已接受的資料類型、應使用的每個資料類型, 以及建議的數量。 建立模型時, 不需要每種資料類型。 資料需求會根據您是建立測試或定型模型而有所不同。

| 資料類型 | 測試的用途 | 數量 | 用於定型 | 數量 |
|-----------|-----------------|----------|-------------------|----------|
| [音訊](#audio-data-for-testing) | 是<br>用於視覺檢查 | 5 + 音訊檔案 | 否 | N/a |
| [音訊 + 人為標記的文字記錄](#audio--human-labeled-transcript-data-for-testingtraining) | 是<br>用來評估精確度 | 0.5-5 小時的音訊 | 是 | 1-1000 小時的音訊 |
| [相關文字](#related-text-data-for-training) | 否 | N/a | 是 | 1-200 MB 的相關文字 |

檔案應該依類型分組至資料集, 並以 zip 檔案的形式上傳。 每個資料集只能包含單一資料類型。

## <a name="upload-data"></a>上傳資料

當您準備好上傳資料時, 請按一下 **[上傳資料**] 以啟動精靈, 並建立您的第一個資料集。 系統會要求您選取資料集的語音資料類型, 然後才允許您上傳資料。

![從語音入口網站選取音訊](./media/custom-speech/custom-speech-select-audio.png)

您上傳的每個資料集都必須符合所選資料類型的需求。 在您的資料上傳之前, 請務必正確地設定其格式。 這可確保自訂語音服務會正確地處理資料。 下列各節會列出需求。

在您的資料集上傳之後, 您有幾個選項:

* 您可以流覽至 [**測試**] 索引標籤, 並以視覺化方式檢查 [僅音訊] 或 [音訊 + 人為標記的轉譯資料]
* 您可以流覽至 [**定型**] 索引標籤, 並使用音訊 + 人為轉譯資料或相關的文字資料來定型自訂模型。

## <a name="audio-data-for-testing"></a>用於測試的音訊資料

音訊資料是測試 Microsoft 基準語音轉換文字模型或自訂模型精確度的最佳做法。 請記住, 音訊資料是用來檢查特定模型效能的語音準確度。 如果您想要量化模型的精確度, 請使用[音訊 + 人為標記](#audio--human-labeled-transcript-data-for-testingtraining)的轉譯資料。

使用此表格來確保您的音訊檔案格式正確, 可與自訂語音搭配使用:

| 內容 | 值 |
|----------|-------|
| 檔案格式 | RIFF (WAV) |
| 採樣速率 | 8000 hz 或 16000 Hz |
| 頻道 | 1 (mono) |
| 每一音訊的最大長度 | 2 小時 |
| 樣本格式 | PCM，16 位元 |
| 封存格式 | .zip |
| 封存大小上限 | 2 GB |

如果您的音訊不符合這些內容, 或您想要檢查其是否存在, 建議您下載[sox](http://sox.sourceforge.net)以檢查或轉換音訊。 以下是如何透過命令列來完成每個活動的一些範例:

| activities | 描述 | Sox 命令 |
|----------|-------------|-------------|
| 檢查音訊格式 | 使用此命令來檢查音訊檔案格式。 | `sox --i <filename>` |
| 轉換音訊格式 | 使用此命令可將音訊檔案轉換成單一通道, 16 位, 16 KHz。 | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>音訊 + 用於測試/定型的人為標記的文字記錄資料

若要在處理您的音訊檔案時測量 Microsoft 的語音轉換文字精確度準確度, 您必須提供人為標記的轉譯 (單字) 以進行比較。 雖然人加上標記的轉譯經常耗用時間, 但還是必須評估精確度, 並針對您的使用案例來定型模型。 請記住, 辨識的改進只會與所提供的資料一樣好。 基於這個理由, 請務必只上傳高品質的文字記錄。  

| 屬性 | 值 |
|----------|-------|
| 檔案格式 | RIFF (WAV) |
| 採樣速率 | 8000 hz 或 16000 Hz |
| 頻道 | 1 (mono) |
| 每一音訊的最大長度 | 60秒 |
| 樣本格式 | PCM，16 位元 |
| 封存格式 | .zip |
| 最大 zip 大小 | 2 GB |

若要解決 word 刪除或替代等問題, 需要大量的資料來改善辨識。 一般來說, 建議您提供大約10到1000小時音訊的單字文字轉譯。 所有 WAV 檔案的文字記錄應包含在單一純文字檔案中。 文字記錄檔案的每一行都應包含其中一個音訊檔案的名稱，然後後面接著相對應的文字記錄。 檔案名稱和文字記錄應該以定位字元 (\t) 分隔。

  例如:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> 文字記錄應使用 UTF-8 位元組順序標記 (BOM) 編碼。

文字記錄會經過文字正規化，以便系統進行處理。 不過，有一些重要的正規化，必須由使用者在將資料上傳到自訂語音服務_之前_完成。 若要在準備轉譯時使用適當的語言, 請參閱[如何建立人為標記](how-to-custom-speech-human-labeled-transcriptions.md)的轉譯

收集音訊檔案和對應的轉譯之後, 請先將其封裝為單一 .zip 檔案, 然後再上傳至自訂語音入口網站。 這是包含三個音訊檔案和人為標記轉譯檔案的範例資料集:

![從語音入口網站選取音訊](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>定型的相關文字資料

如果您的產品名稱或功能是唯一的, 而且您想要確保它們能正確辨識, 請務必包含定型的相關文字資料。 可以提供兩種類型的相關文字資料來改善辨識:

| 資料類型 | 此資料如何改善辨識 |
|-----------|------------------------------------|
| 語句和 (或) 句子 | 當辨識產品名稱或句子內容中的產業特定詞彙時, 這些可以改善正確性。 |
| 發音 | 這些可以改善不常見詞彙、縮略字或其他單字的發音, 而非定義的發音。 |

語句可以提供為單一或多個文字檔。 文字資料與所要讀出的內容越接近, 精確度的改善可能性就越高。 發音應以單一文字檔的形式提供。 所有專案都可以封裝成單一 zip 檔案, 並上傳至自訂語音入口網站。

### <a name="guidelines-to-create-an-utterances-file"></a>建立語句檔案的指導方針

若要使用相關的文字建立自訂模型, 您必須提供範例語句的清單。 這些語句不需要是完整句子或文法正確, 但必須精確地反映您預期在生產環境中的語音輸入。 如果您想要特定詞彙的權數增加, 可以將數個句子新增至包含這些特定詞彙的相關資料檔。

請使用此資料表來確保語句的相關資料檔案格式正確:

| 屬性 | 值 |
|----------|-------|
| 文字編碼 | UTF-8 BOM |
| 每一行的語句數目 | 1 |
| 檔案大小上限 | 200 MB |

此外, 您會想要考慮下列限制:

* 避免重複字元超過四次。 例如: "aaaa" 或 "uuuu"。
* 請勿在 U + 00A1 的前面使用特殊字元或 UTF-8 字元。
* Uri 將會遭到拒絕。

### <a name="guidelines-to-create-a-pronunciation-file"></a>建立發音檔案的指導方針

如果有不尋常的詞彙, 而您的使用者將會遇到或使用標準發音, 您可以提供自訂發音檔案來改善辨識。

> [!IMPORTANT]
> 不建議使用這項功能來改變常用單字的發音。

這包括說語句的範例, 以及每個的自訂發音:

| 已辨識/已顯示的表單 | 口語形式 |
|--------------|--------------------------|
| 3CPO | three c p o |  
| CNTK | c n t k |
| IEEE | i triple e |

說出的形式就是語音順序。它可以包含字母、字組、音節或全部三種組合。

自訂發音提供英文 (en-us) 和德文 (de)。 下表依語言顯示支援的字元:

| 語言 | 地區設定 | 人物 |
|----------|--------|------------|
| 英文 | zh-TW | a、b、c、d、e、f、g、h、i、j、k、l、m、n、o、p、q、r、s、t、u、v、w、x、y、z |
| 德文 | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

請使用此表格來確保發音的相關資料檔案格式正確。 發音檔案很小, 而且不應該超過幾個 Kb。

| 內容 | 值 |
|----------|-------|
| 文字編碼 | UTF-8 BOM (ANSI 也支援英文) |
| 每行的發音數 | 1 |
| 檔案大小上限 | 1 MB (免費層為 1 KB) |

## <a name="next-steps"></a>後續步驟

* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [訓練您的模型](how-to-custom-speech-train-model.md)
* [部署您的模型](how-to-custom-speech-deploy-model.md)
