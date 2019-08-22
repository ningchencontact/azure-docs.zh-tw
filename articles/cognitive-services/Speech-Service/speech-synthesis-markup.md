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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 12d556fd9c37b83a919b830d155250e9eaa64128
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624248"
---
# <a name="speech-synthesis-markup-language-ssml"></a>語音合成標記語言 (SSML)

語音合成標記語言 (SSML) 是以 XML 為基礎的標記語言, 可讓開發人員指定如何使用文字轉換語音服務, 將輸入文字轉換成合成語音。 相較于純文字, SSML 讓開發人員能夠微調文字到語音轉換輸出的音調、發音、說話速度、音量和更多。 一般標點符號 (例如在句號之後暫停), 或使用正確的聲調 (當句子以問號結尾時) 會自動處理。

SSML 的語音服務執行是以全球資訊網協會的[語音合成標記語言1.0 版](https://www.w3.org/TR/speech-synthesis)為基礎。

> [!IMPORTANT]
> 中文、日文和韓文字元的計費方式為兩個字元。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>標準、類神經和自訂語音

從標準和類神經語音中選擇, 或為您的產品或品牌建立專屬的自訂語音。 75 + standard 語音提供45以上的語言和地區設定, 而5類神經語音則提供4種語言和地區設定。 如需支援的語言、地區設定和語音 (神經和標準) 的完整清單，請參閱[語言支援](language-support.md)。

若要深入瞭解標準、類神經和自訂語音, 請參閱[文字轉換語音的總覽](text-to-speech.md)。

## <a name="special-characters"></a>特殊字元

使用 SSML 轉換文字到合成的語音時, 請記住, 就像使用 XML 一樣, 特殊字元 (例如引號、撇號和方括弧) 必須經過轉義。 如需詳細資訊, [請參閱可延伸標記語言 (XML) (XML) 1.0:附錄 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支援的 SSML 元素

每個 SSML 檔都是使用 SSML 元素 (或標記) 所建立。 這些元素可用來調整音調、韻律、音量等等。 下列各節將詳細說明每個專案的使用方式, 以及當元素為必要或選擇性時。  

> [!IMPORTANT]
> 別忘了在屬性值前後使用雙引號。 格式正確且有效的 XML 的標準需要以雙引號括住屬性值。 例如, `<prosody volume="90">`是格式正確且有效的元素, 但`<prosody volume=90>`不是。 SSML 可能無法辨識不是以引號括住的屬性值。

## <a name="create-an-ssml-document"></a>建立 SSML 檔

`speak`是根項目, 而且是所有 SSML 檔的**必要**專案。 `speak`元素包含重要資訊, 例如版本、語言和標記詞彙定義。

**語法**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| 版本 | 表示用來解讀檔標記的 SSML 規格版本。 目前的版本為1.0。 | 必要項 |
| xml: lang | 指定根文檔的語言。 此值可包含小寫、兩個字母的語言代碼 (例如**en**), 或語言代碼和大寫國家/地區 (例如, **en-us**)。 | 必要項 |
| xmlns | 指定檔的 URI, 以定義 SSML 檔的標記詞彙 (元素類型和屬性名稱)。 目前的 URI 是 https://www.w3.org/2001/10/synthesis 。 | 必要項 |

## <a name="choose-a-voice-for-text-to-speech"></a>選擇文字轉換語音的語音

需要`voice`元素。 它是用來指定文字轉換語音所使用的語音。

**語法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| name | 識別文字到語音轉換輸出所使用的語音。 如需支援的語音的完整清單, 請參閱[語言支援](language-support.md#text-to-speech)。 | 必要項 |

**範例**

> [!NOTE]
> 這個範例會使用`en-US-Jessa24kRUS`語音。 如需支援的語音的完整清單, 請參閱[語言支援](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多重音源

`speak`在元素內, 您可以為文字到語音轉換輸出指定多個語音。 這些語音可以採用不同的語言。 針對每個聲音, 文字必須包裝在`voice`元素中。

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| name | 識別文字到語音轉換輸出所使用的語音。 如需支援的語音的完整清單, 請參閱[語言支援](language-support.md#text-to-speech)。 | 必要項 |

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>調整說話樣式

> [!IMPORTANT]
> 這項功能只適用于神經語音。

根據預設, 文字轉換語音服務會針對標準和類神經語音使用中性說話樣式來合成文字。 使用神經語音時, 您可以調整說話樣式, 以使用`<mstts:express-as>`元素表達 cheerfulness、理解或情感。 這是 Azure 語音服務特有的選擇性元素。

目前, 這些類神經語音支援說話的樣式調整:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

變更會在句子層級套用, 而樣式會因語音而有所不同。 如果樣式不受支援, 服務將會以預設的中性說話樣式傳回語音。

**語法**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| type | 指定說話的樣式。 目前, 說話樣式是語音特有的。 | 如果要調整類神經語音的說話樣式, 則為必要。 如果使用`mstts:express-as`, 則必須提供類型。 如果提供了不正確值, 則會忽略此元素。 |

使用此表格來判斷每個類神經語音支援哪些說話樣式。

| 語音 | Type | 描述 |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | 表達正面且滿意的表情 |
| | type=`empathy` | 表達管也和認知的意義 |
| | type=`chat` | 以偶爾、寬鬆的語調說話 |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | 表達類似于新聞廣播的正式音調 |
| | type=`sentiment` | 傳達觸控訊息或故事 |

**範例**

這個 SSML 程式碼片段說明如何`<mstts:express-as>`使用專案將說話風格變更為。 `cheerful`

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>新增或移除中斷/暫停

使用元素`break`在單字之間插入暫停 (或中斷), 或防止文字轉換語音服務自動暫停。

> [!NOTE]
> 使用此元素可覆寫單字或片語的文字轉換語音 (TTS) 的預設行為 (如果該單字或片語的合成語音非自然)。 設定`strength` 為`none`以防止韻律中斷, 這會由文字轉換語音服務自動插入。

**語法**

```xml
<break strength="string" />
<break time="string" />
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| 程度 | 使用下列其中一個值, 指定暫停的相對持續時間:<ul><li>無</li><li>x-弱式</li><li>弱</li><li>中 (預設值)</li><li>強</li><li>x-強式</li></ul> | 選擇性 |
| time | 指定暫停的絕對持續時間 (以秒或毫秒為單位)。 有效值為2和500的範例 | 選擇性 |

| 程度 | 描述 |
|----------|-------------|
| 無; 如果未提供任何值, 則為 | 0毫秒 |
| x-弱式 | 250毫秒 |
| 弱 | 500 毫秒 |
| 中 | 750 毫秒 |
| 強 | 1000毫秒 |
| x-強式 | 1250毫秒 |


**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p`和`s`元素分別用來表示段落和句子。 如果沒有這些元素, 文字轉換語音服務會自動決定 SSML 檔的結構。

`prosody` `break` `phoneme` `audio` `say-as` `sub`元素可能包含文字和下列元素:、、、、 、`mstts:express-as`、和。`s` `p`

`phoneme` `break` `audio` `prosody` `say-as` `mstts:express-as`元素可能包含文字和下列元素:、、、、、和`sub`。 `s`

**語法**

```XML
<p></p>
<s></s>
```

**範例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素來改善發音

`ph`元素用於 SSML 檔中的語音發音。 `ph`元素只能包含文字, 沒有其他元素。 一律提供人類可讀的語音做為回復。

語音字母由電話組成, 其由字母、數位或字元組成, 有時會組合。 每個電話都會說明語音的獨特聲音。 這與拉丁字母相較之下, 其中任何字母可能代表多個說話的聲音。 請考慮字母 "c" 在「糖果」和「停止」單字中的不同發音, 或在「內容」和「那些」單字中, 字母組合「th」的不同發音。

**語法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| 拉丁字母 | 指定合成`ph`屬性中字串的發音時, 所要使用的語音字母。 指定字母的字串必須以小寫字母指定。 以下是您可以指定的可能字母。<ul><li>.ipa &ndash;國際語音字母</li><li>sapi &ndash;語音 API 電話集</li><li>ups &ndash;通用電話集</li></ul>此字母僅適用于元素中的音素。 如需詳細資訊, 請參閱[語音字母參考](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)。 | 選擇性 |
| 三相 | 包含電話的字串, 指定`phoneme`元素中單字的發音。 如果指定的字串包含無法辨識的手機, 文字轉換語音 (TTS) 服務會拒絕整個 SSML 檔, 而且不會產生任何在檔中指定的語音輸出。 | 如果使用音素, 則為必要。 |

**範例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>調整韻律

`prosody`元素可用來指定文字到語音轉換輸出的音調、countour、範圍、速率、持續時間和音量的變更。 `phoneme` `break` `p` `audio` `prosody` `say-as`元素可能包含文字和下列元素:、、、、 、`sub`、和。`s` `prosody`

由於韻律屬性的值可能會隨著寬範圍而有所不同, 因此語音辨識器會將指派的值解釋為所選語音的實際韻律值應為的建議。 文字轉換語音服務會限制或替代不支援的值。 不支援值的範例是 1 MHz 或磁片區120。

**語法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| 介紹 | 表示文字的基準間距。 您可以用下列方式表達音調:<ul><li>絕對值, 以數位表示, 後面接著 "Hz" (赫茲)。 例如, 600Hz。</li><li>以數位表示的相對值, 前面加上 "+" 或 "-", 後面接著 "Hz" 或 "st", 以指定要變更音調的數量。 例如: + 80Hz 或-2st。 "St" 表示變更單位是 semitone, 這是標準 diatonic 尺規上的一半色調 (半步驟)。</li><li>常數值:<ul><li>x-低</li><li>低</li><li>中</li><li>高</li><li>x-高</li><li>預設值</li></ul></li></ul>. | 選擇性 |
| 輪廓 | 類神經語音不支援等高線。 [等高線] 代表語音內容在語音輸出中指定時間位置的 [音調] 變更, 做為目標陣列。 每個目標都是由一組參數配對所定義。 例如: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每一組參數中的第一個值會指定音調變更的位置, 以文字持續時間的百分比表示。 第二個值指定要增加或減少音調的數量, 使用相對值或用於音調的列舉值 (請參閱`pitch`)。 | 選擇性 |
| range  | 值, 表示文字的音調範圍。 您可以使用`range`相同的絕對值、相對值或用來描述`pitch`的列舉值來表示。 | 選擇性 |
| 增長率  | 表示文字的說話速率。 您可以表達`rate`如下:<ul><li>相對值, 以做為預設值之乘數的數位來表示。 例如, 值*1*會導致速率不會變更。 值為 *.5*會產生速率的減半。 值為*3*會產生速率的增加三倍。</li><li>常數值:<ul><li>x-慢</li><li>slow</li><li>中</li><li>地</li><li>x-快速</li><li>預設值</li></ul></li></ul> | 選擇性 |
| 持續時間  | 語音合成 (TTS) 服務讀取文字 (以秒或毫秒為單位) 時所經過的時間長度。 例如, 2*秒*或*1800ms*。 | 選擇性 |
| 磁碟區  | 表示說話語音的音量層級。 您可以將磁片區表示為:<ul><li>絕對值, 以0.0 到100.0 範圍內的數位表示, 從*quietest*到*loudest*。 例如, 75。 預設值為100.0。</li><li>以數位表示的相對值, 其前面加上 "+" 或 "-", 以指定要變更磁片區的數量。 例如 + 10 或-5.5。</li><li>常數值:<ul><li>安靜</li><li>x-soft</li><li>軟</li><li>中</li><li>很</li><li>x-大聲</li><li>預設值</li></ul></li></ul> | 選擇性 |

### <a name="change-speaking-rate"></a>改變說話速度

說話速率可以在單字或句子層級套用至標準語音。 而說話的速率只能套用至句子層級的類神經語音。

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>變更音量

磁片區變更可以套用至單字或句子層級的標準語音。 而磁片區變更只能套用至句子層級的類神經語音。

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>變更音高

音調變更可以套用至單字或句子層級的標準語音。 而音調變更只能套用至句子層級的類神經語音。

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>變更音高結構

> [!IMPORTANT]
> 類神經語音不支援音調輪廓變更。

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>新增錄製的音訊

`audio`是選擇性元素, 可讓您將 MP3 音訊插入 SSML 檔中。 音訊元素的主體可能包含純文字或 SSML 標記, 如果音訊檔無法使用或播放, 就會說出來。 `audio`此外, 元素可以包含文字和下列元素: `prosody` `s` `audio`、 `break`、 `p`、、 `phoneme`、 `say-as`、和`sub`。

SSML 檔中包含的任何音訊都必須符合下列需求:

* MP3 必須裝載在可存取網際網路的 HTTPS 端點上。 需要 HTTPS, 而且裝載 MP3 檔案的網域必須提供有效、受信任的 SSL 憑證。
* MP3 必須是有效的 MP3 檔案 (MPEG v2)。
* 位元速率必須是 48 kbps。
* 取樣速率必須是 16000 Hz。
* 單一回應中所有文字和音訊檔案的總時間總和不能超過 90 (90) 秒。
* MP3 不得包含任何客戶特定或其他機密資訊。

**語法**

```xml
<audio src="string"/></audio>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| src | 指定音訊檔案的位置/URL。 | 如果在您的 SSML 檔中使用音訊元素, 則為必要專案。 |

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>新增背景音訊

`mstts:backgroundaudio`元素可讓您將背景音訊新增至 SSML 檔 (或混合具有文字轉換語音的音訊檔案)。 有`mstts:backgroundaudio`了, 您可以在背景中迴圈音訊檔案、從文字到語音的開頭淡入, 然後在文字轉換語音的結尾淡出。

如果提供的背景音訊短于文字轉換語音或淡出, 則會迴圈。 如果超過文字轉換語音, 則會在淡出完成時停止。

每一份 SSML 檔只能有一個背景音訊檔案。 不過, 您可以在`audio`專案`voice`內散置標記, 以在 SSML 檔中新增其他音訊。

**語法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| src | 指定背景音訊檔案的位置/URL。 | 如果您在 SSML 檔中使用背景音訊, 則為必要項。 |
| 磁碟區 | 指定背景音訊檔案的磁片區。 **接受**的值`0` : `100`包含 (含)。 預設值為 `1`。 | 選擇性 |
| fadein | 指定背景音訊淡入的持續時間 (以毫秒為單位)。 預設值為`0`, 這相當於「不淡入」。 **接受**的值`0` : `10000`包含 (含)。  | 選擇性 |
| fadeout | 指定背景音訊的持續時間 (以毫秒為單位)。 預設值為`0`, 這相當於 [不淡出]。**接受**的值`0` : `10000`包含 (含)。  | 選擇性 |

**範例**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>後續步驟

* [語言支援：語音、地區設定、語言](language-support.md)
