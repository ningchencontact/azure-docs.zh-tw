---
title: AudioInputStream 概念
description: AudioInputStream API 的功能概觀。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: b3e12fbc616c8d67b557102c6094467e119a23f1
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281900"
---
# <a name="about-the-audio-input-stream-api"></a>關於音訊輸入資料流 API

**音訊輸入資料流** API 提供將音訊資料流串流到辨識器中的方法，而不需使用麥克風或輸入檔 API。

## <a name="api-overview"></a>API 概觀

API 使用兩個元件：`AudioInputStream` (未經處理的音訊資料) 和 `AudioInputStreamFormat`。

`AudioInputStreamFormat` 可定義音訊資料的格式。 這可比喻為 Windows 上 wav 檔的標準 `WAVEFORMAT` 結構。

  - `FormatTag`

    音訊的格式。 語音 SDK 目前僅支援`format 1` (PCM - little-endian)。

  - `Channels`

    頻道的數目。 目前的語音服務僅支援一個頻道 (單聲道) 音訊資料。

  - `SamplesPerSec`

    取樣率。 典型的麥克風錄音每秒有 16000 個樣本。

  - `AvgBytesPerSec`

    每秒的平均位元組，計算方式為 `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`。 若為使用可變位元速率的音訊資料流，每秒的平均位元組可以不同。

  - `BlockAlign`

    單一框架的大小，計算方式為 `Channels * ceil(wBitsPerSample, 8)`。 因為進行填補，所以實際值可能高於此值。

  - `BitsPerSample`

    每個樣本的位元數。 典型的音訊資料流會使用每個樣本 16 位元 (CD 品質)。

您的自訂資料流配接器將會覆 `AudioInputStream` 基底類別。 此配接器必須實作下列函式：

   - `GetFormat()`

     呼叫此函式，以取得音訊資料流的格式。 它會取得 AudioInputStreamFormat 緩衝區的指標。

   - `Read()`

     呼叫此函式，以從音訊資料流取得資料。 第一個參數是緩衝區的指標，而音訊資料會複製到該緩衝區中。 第二個參數是緩衝區的大小。 函數會傳回已複製到緩衝區的位元組數目。 `0` 的傳回值表示資料流結尾。

   - `Close()`

     呼叫此函式，以關閉音訊資料流。

## <a name="usage-examples"></a>使用方式範例

一般而言，使用音訊輸入資料流時，需執行下列步驟：

  - 識別音訊資料流的格式。 SDK 和語音服務必須支援此格式。 目前支援下列組態：

    一個音訊格式標記 (PCM)、一個頻道、每秒 16000 個樣本、每秒 32000 個位元組、兩個區塊對齊 (一個樣本 16 位元，包含填補)、每個樣本 16 位元

  - 確定您的程式碼可以提供有關上述規則的未經處理音訊資料。 如果您的音訊來源資料不符合支援的格式，則必須將音訊轉碼成所需的格式。

  - 從 `AudioInputStream` 衍生您的自訂音訊輸入資料流類別。 實作 `GetFormat()`、`Read()` 和 `Close()` 作業。 確切的函式簽章視語言而定，但程式碼看起來類似此程式碼範例：

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - 使用您的音訊輸入資料流：

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - 在某些語言中，必須在辨識完成後明確刪除 `contosoStream`。 您無法在完整輸入讀取之前釋出 AudioStream。 在使用 `StopContinuousRecognitionAsync` 和 `StopContinuousRecognitionAsync` 的案例中，需要此範例中所說明的概念：

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstart-csharp-dotnet-windows.md) (英文)
