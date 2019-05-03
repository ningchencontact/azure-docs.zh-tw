---
title: 語音合成標記語言 (SSML)-語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音合成標記語言控制文字轉語音的發音和韻律。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 9871e0106ee6caf11c5a1e24459fbd2044f5f3d7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021446"
---
# <a name="speech-synthesis-markup-language-ssml"></a>語音合成標記語言 (SSML)

語音合成標記語言 (SSML) 是一種 XML 架構的標記語言，可控制文字轉語音的發音和*韻律*。 韻律是指說話時的節奏和音高，甚至也可以稱之音樂。 您可以指定文字的發音、提供解釋數字、插入暫停、控制音高、音量和速率等提示。 如需詳細資訊，請參閱[語音合成標記語言 (SSML) 版本 1.0](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)。 

如需支援的語言、地區設定和語音 (神經和標準) 的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。

下列各節提供常見語音合成工作的範例。

## <a name="adjust-speaking-style-for-neural-voices"></a>調整類神經的語音的語音的模式

您可以使用 SSML 調整講話樣式，當使用其中一個的類神經的語音。

根據預設，文字轉換語音服務會合成中性樣式中的文字。 類神經語音延伸使用的 SSML`<mstts:express-as>`將文字轉換成在不同說話的合成語音的項目設定樣式。 目前，這些語音只支援樣式標籤：

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural` 。

說到樣式變更可以套用在句子層級。 樣式會因語音。 如果不支援樣式型別，則服務會傳回合成的語音，做為預設中性樣式。

| 語音 | Style | 描述 | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | 表示情感是正面的並祝您 |
| | type=`empathy` | 表示的關懷，以及了解 |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | 表示正式音，類似於新聞廣播 |
| | type=`sentiment ` | 傳達碰到的訊息] 或 [劇本 |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>加入中斷符號
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>改變說話速度

讀出速率可以套用至 word 或句子層級的標準語音。 而說到速率只可以套用至句子層級的類神經語音。

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>發音
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>變更音量

磁碟區的變更可以套用至 word 或句子層級的標準語音。 而磁碟區的變更只會套用至句子層級的類神經語音。

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>變更音高

字幅變更可以套用至 word 或句子層級的標準語音。 而只能以類神經語音句子層級套用字幅變更。

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>變更音高結構

> [!IMPORTANT]
> 類神經的語音的不支援字幅分佈變更。

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>使用多重音源
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>後續步驟

* [語言支援：語音、地區設定、語言](language-support.md)
