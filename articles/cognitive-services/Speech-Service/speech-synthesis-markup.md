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
ms.openlocfilehash: 8285a76f8cd07863874f9c8e8eebe96f1cb968dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604826"
---
# <a name="speech-synthesis-markup-language-ssml"></a>語音合成標記語言 (SSML)

語音合成標記語言 (SSML) 是一種以 XML 為基礎的標記語言，可讓開發人員指定如何輸入的文字會轉換成使用文字轉換語音服務的合成語音。 相較於純文字，SSML 可讓開發人員来微調的音調、 發音讀出速率、 磁碟區，以及多個文字轉換語音輸出。 一般標點符號，例如暫停期間過後，或問號結尾的句子時，請使用正確的聲調會自動處理。

語音服務所實作的 SSML 根據全球資訊網協會[語音合成標記語言版本 1.0](https://www.w3.org/TR/speech-synthesis)。

> [!IMPORTANT]
> 中文、 日文和韓文字元會列入計費的兩個字元。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>Standard、 類神經，和自訂的語音

從標準和類神經的語音，選擇或建立您自己自訂的語音特有您的產品或品牌。 75 個以上標準語音有 45 個以上的語言和地區設定，和 5 的類神經語音有 4 個語言和地區設定。 如需支援的語言、地區設定和語音 (神經和標準) 的完整清單，請參閱[語言支援](language-support.md)。

若要深入了解類神經，標準和自訂的語音，請參閱[文字轉換語音概觀](text-to-speech.md)。

## <a name="supported-ssml-elements"></a>支援的 SSML 元素

每個 SSML 文件建立 SSML 項目 （或標籤）。 這些項目用來調整字幅、 韻律、 磁碟區和更多功能。 下列各節詳細說明如何使用每個項目，以及當項目是必要或選擇性。  

> [!IMPORTANT]
> 別忘了使用雙引號括住屬性值周圍。 格式正確且有效的 XML 標準需要以雙引號括住屬性值。 例如，`<prosody volume="90">`是格式正確且有效的項目，但`<prosody volume=90>`不是。 SSML 可能無法辨識的不是以引號括住屬性值。

## <a name="create-an-ssml-document"></a>建立 SSML 文件

`speak` 根項目，而**必要**所有 SSML 文件。 `speak`項目包含重要的資訊，例如版本、 語言和標記詞彙定義。

**語法**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| version | 表示用來解譯的文件標記的 SSML 規格版本。 目前的版本為 1.0。 | 必要項 |
| Xml: lang | 指定的根文件的語言。 值可包含小寫字母、 兩個字母的語言代碼 (例如**en**)，或語言的程式碼和大寫的國家/地區 (比方說， **EN-US**)。 | 必要項 |
| xmlns | 指定定義標記 （項目型別和屬性名稱） 的詞彙 SSML 文件的文件的 URI。 目前的 URI 是 https://www.w3.org/2001/10/synthesis 。 | 必要項 |

## <a name="choose-a-voice-for-text-to-speech"></a>選擇文字轉換語音的聲音

`voice`是必要元素。 它用來指定用於文字轉換語音的聲音。

**語法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| name | 識別用於文字轉換語音輸出的語音。 支援的語音的完整清單，請參閱 <<c0> [ 語言支援](language-support.md#text-to-speech)。 | 必要 |

**範例**

> [!NOTE]
> 這個範例會使用`en-US-Jessa24kRUS`語音。 支援的語音的完整清單，請參閱 <<c0> [ 語言支援](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多重音源

內`speak`項目，您可以指定多個語音的文字轉換語音輸出。 這些語音可位於不同的語言。 每個語音中，文字必須包裝在`voice`項目。

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| name | 識別用於文字轉換語音輸出的語音。 支援的語音的完整清單，請參閱 <<c0> [ 語言支援](language-support.md#text-to-speech)。 | 必要 |

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

## <a name="adjust-speaking-styles"></a>調整說話的樣式

> [!IMPORTANT]
> 這項功能只適用於類神經的語音。

根據預設，文字轉換語音服務會合成中性的語音模式用於標準和類神經語音的文字。 使用類神經的語音，您可以調整表達 cheerfulness、 有同理心或使用情感的說話`<mstts:express-as>`項目。 這是選擇性的項目唯一 Azure 語音服務。

目前，講話樣式調整這些類神經的語音支援：
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

變更會套用在句子層級和樣式會因語音。 如果樣式不支援，服務會傳回語音在預設的中性講話樣式。

**語法**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| type | 指定語音模式。 目前，講話樣式是特定的語音。 | 如果調整的類神經的語音的語音模式，需要。 如果使用`mstts:express-as`，則必須提供型別。 如果提供了無效的值，則會忽略這個項目。 |

您可以使用此表格來判斷哪些講話樣式支援每個類神經的語音。

| 語音 | type | 描述 |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | 表示情感是正面的並祝您 |
| | type=`empathy` | 表示的關懷，以及了解 |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | 表示正式音，類似於新聞廣播 |
| | type=`sentiment` | 傳達碰到的訊息] 或 [劇本 |

**範例**

此 SSML 程式碼片段說明如何`<mstts:express-as>`項目用來變更要讀出的樣式`cheerful`。

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

使用`break`字詞之間插入暫停 （或中斷），或防止自動加入文字轉換語音服務暫停的項目。

> [!NOTE]
> 您可以使用此項目覆寫預設行為的文字轉換語音 (text-to-speech，TTS) 的單字或片語的單字或片語的合成的語音聽起來不自然。 設定`strength`至`none`防止韻律中斷，會自動插入 text>-語音服務。

**語法**

```xml
<break strength="string" />
<break time="string" />
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| 強度 | 指定相對的持續期間的暫停，使用下列值之一：<ul><li>None</li><li>x 弱式</li><li>弱式</li><li>Medium （預設值）</li><li>強式</li><li>x 強式</li></ul> | 選擇性 |
| time | 指定絕對的持續時間的秒或毫秒的暫停時間。 有效值範例包括 2 秒和 500 | 選擇性 |

| 強度 | 描述 |
|----------|-------------|
| 無，或如果未提供值 | 0 毫秒 |
| x 弱式 | 250 毫秒 |
| 弱式 | 500 毫秒 |
| 中 | 750 毫秒 |
| 強式 | 1000 毫秒 |
| x 強式 | 1250 毫秒 |


**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p` 和`s`元素用來分別代表段落和句子。 如果沒有這些項目，文字轉換語音服務會自動決定 SSML 文件的結構。

`p`項目可以包含文字和下列元件： `audio`， `break`， `phoneme`， `prosody`， `say-as`， `sub`， `mstts:express-as`，和`s`。

`s`項目可以包含文字和下列元件： `audio`， `break`， `phoneme`， `prosody`， `say-as`， `mstts:express-as`，以及`sub`。

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

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素改善發音

`ph`項目用於 SSML 文件中的語音發音。 `ph`元素只能包含文字，沒有其他項目。 永遠提供人類看得懂的語音，做為後援。

語音字母所組成電話、 字母、 數字或有時在組合中的字元所組成。 每個電話描述唯一語音的聲音。 這是相較於拉丁字母，其中的任何字母可能表示多個語音的聲音。 請考慮不同的發音的字母"c"中的文字 「 指標 」 和 「 終止 」 或不同的發音字母組合的"th"在單字"thing"和""。

**語法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| 字母 | 指定要在由合成發音的字串中的語音字母`ph`屬性。 指定字母的字串必須指定以小寫字母。 以下是可能的音義符號，您可以指定。<ul><li>ipa&ndash;國際音標</li><li>sapi&ndash;設定電話語音 API</li><li>ups&ndash;通用的電話設定</li></ul>字母只適用於項目中的音素。 如需詳細資訊，請參閱 <<c0> [ 語音字母參考](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)。 | 選擇性 |
| ph | 字串，包含指定字的發音的手機`phoneme`項目。 如果指定的字串會包含無法辨識的手機，文字轉換語音 (text-to-speech，TTS) 服務會拒絕整個 SSML 文件，並產生沒有任何指定文件中的語音輸出。 | 如果使用音素，需要。 |

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

`prosody`項目用來指定字幅、 countour、 範圍、 速率、 持續時間，以及文字轉換語音輸出的磁碟區的變更。 `prosody`項目可以包含文字和下列元件： `audio`， `break`， `p`， `phoneme`， `prosody`， `say-as`， `sub`，和`s`。

韻律屬性的值可能會不同，透過各種不同，因為語音辨識器會將指派的值解譯為實際選取的語音的韻律值應該是什麼的建議。 文字轉換語音服務限制，或取代不支援的值。 不支援值的範例是 1 MHz 的推銷或 120 的磁碟區。

**語法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**屬性**

| 屬性 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| 音調 | 表示文字的基準字幅。 您可能會表示為點數：<ul><li>絕對值，以數字，後面接著"Hz 」 （赫茲）。 比方說，600 Hz。</li><li>相對的值，表示為數字前面加上"+"或"-"，後面接著"Hz"或"st"，指定的數量和變更的字幅。 例如： +80 Hz 或 2st。 "st"表示變更單位為半音，也就是一半的標準 diatonic 標尺上的色調 （半步驟）。</li><li>常數的值：<ul><li>x-low</li><li>低</li><li>中</li><li>高</li><li>x 高</li><li>預設值</li></ul></li></ul>。 | 選擇性 |
| 分佈 | 分佈不支援的類神經的語音。 Contour 代表音調的語音內容變更為指定的時間位置在語音輸出的目標陣列。 每個目標定義的參數組之集合。 例如: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每個參數集的第一個值會指定位置的字幅變更文字的持續時間的百分比。 第二個值會指定要提高或降低音調、 使用相對值或列舉值的字幅的量 (請參閱`pitch`)。 | 選擇性 |
| range  | 值，表示文字的音調範圍。 您可能會表示`range`使用相同的絕對值，相對的值或列舉值用來描述`pitch`。 | 選擇性 |
| 速率  | 表示文字的讀出速率。 您可能會表示`rate`為：<ul><li>表示做為預設值的倍數的數字的相對值。 例如，值*1*不導致率的任何變更。 值為 *.5*導致減半來計算的費率。 值為*3*導致增加三倍的費率。</li><li>常數的值：<ul><li>x-slow</li><li>slow</li><li>中</li><li>快速</li><li>x-fast</li><li>預設值</li></ul></li></ul> | 選擇性 |
| duration  | 語音時應該經過的時間段合成 (text-to-speech，TTS) 服務讀取文字、 秒或毫秒。 例如， *lt;2s&gt*或是*1800ms*。 | 選擇性 |
| 磁碟區  | 表示語音讀出的音量。 您可能會表示為磁碟區：<ul><li>絕對值，從表示為在範圍中數目 0.0 至 100.0 *quietest*要*當中*。 例如，75。 預設值為 100.0。</li><li>相對的值，表示為數字前面加上"+"或"-"，指定要變更磁碟區的金額。 例如，+ 10 或-5.5。</li><li>常數的值：<ul><li>無訊息</li><li>x-soft</li><li>soft</li><li>中</li><li>雲端</li><li>x 大聲</li><li>預設值</li></ul></li></ul> | 選擇性 |

### <a name="change-speaking-rate"></a>改變說話速度

讀出速率可以套用至 word 或句子層級的標準語音。 而說到速率只可以套用至句子層級的類神經語音。

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

磁碟區的變更可以套用至 word 或句子層級的標準語音。 而磁碟區的變更只會套用至句子層級的類神經語音。

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

字幅變更可以套用至 word 或句子層級的標準語音。 而只能以類神經語音句子層級套用字幅變更。

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
> 類神經的語音的不支援字幅分佈變更。

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

## <a name="next-steps"></a>後續步驟

* [語言支援：語音、地區設定、語言](language-support.md)
