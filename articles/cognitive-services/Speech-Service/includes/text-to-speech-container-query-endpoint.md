---
title: 查詢文字轉換語音容器端點
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491072"
---
容器提供以[REST 為基礎的端點 api](../rest-text-to-speech.md)。 有許多適用于平臺、架構和語言變化的[範例原始程式碼專案](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/)。

使用*標準的文字轉換語音*容器，您應該依賴所下載影像標籤的地區設定和語音。 例如，如果您下載了 `latest` 標記，則預設地區設定會 `en-US` 和 `JessaRUS` 的語音。 然後會[`en-US-JessaRUS`](../language-support.md#standard-voices)`{VOICE_NAME}` 引數。 請參閱以下的範例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

不過，針對*自訂文字轉換語音*，您必須從[自訂語音入口網站](https://aka.ms/custom-voice-portal)取得**語音/型號**。 自訂模型名稱與語音名稱同義。 流覽至 [**定型**] 頁面，並複製要當做 `{VOICE_NAME}` 引數使用的**語音/模型**。
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="自訂語音模型-語音名稱":::

請參閱以下的範例 SSML：

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

讓我們來建立 HTTP POST 要求，並提供一些標頭和資料裝載。 以您自己的值取代 `{VOICE_NAME}` 預留位置。

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

此命令：

* 為 `speech/synthesize/cognitiveservices/v1` 端點建立 HTTP POST 要求。
* 指定 `Accept` 標頭 `audio/*`
* 指定 `application/ssml+xml`的 `Content-Type` 標頭，如需詳細資訊，請參閱[要求主體](../rest-text-to-speech.md#request-body)。
* 指定 `riff-16khz-16bit-mono-pcm`的 `X-Microsoft-OutputFormat` 標頭，如需更多選項，請參閱[音訊輸出](../rest-text-to-speech.md#audio-outputs)。
* 將 `{VOICE_NAME}` 傳送給端點的[語音合成標記語言（SSML）](../speech-synthesis-markup.md)要求。