---
title: 語音合成標記語言 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音合成標記語言控制文字轉語音的發音和韻律。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: f6a2d6a35200bc4dec169aae72415c1c2904c465
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341128"
---
# <a name="speech-synthesis-markup-language"></a>語音合成標記語言

語音合成標記語言 (SSML) 是一種 XML 架構的標記語言，可控制文字轉語音的發音和*韻律*。 韻律是指說話時的節奏和音高，甚至也可以稱之音樂。 您可以指定文字的發音、提供解釋數字、插入暫停、控制音高、音量和速率等提示。 如需詳細資訊，請參閱[語音合成標記語言 (SSML) 版本 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/)。

如需支援的語言、地區設定和語音 (神經和標準) 的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。

下列各節提供常見語音合成工作的範例。

>[!IMPORTANT]
> 韻律標記目前僅適用於標準語音。

## <a name="add-a-break"></a>加入中斷符號
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>改變說話速度
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>發音
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>變更音量
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>改變音高
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>變更音高結構
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>使用多重音源
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>後續步驟

* [語言支援：語音、地區設定、語言](language-support.md)
