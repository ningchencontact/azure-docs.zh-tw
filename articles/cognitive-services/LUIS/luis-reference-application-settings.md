---
title: 應用程式設定
titleSuffix: Azure Cognitive Services
description: 了解語言了解應用程式的應用程式設定。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: a8a1f6fe9372d013d310c557161ceb813b8a478b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163642"
---
# <a name="application-settings"></a>應用程式設定

這些應用程式設定會儲存在[匯出](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)應用程式並[更新](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)的 REST api。 變更您的應用程式版本設定，則重設未定型為您的應用程式訓練狀態。

|設定|預設值|注意|
|--|--|--|
|NormalizePunctuation|True|移除標點符號。|
|NormalizeDiacritics|True|移除變音符號。|

## <a name="diacritics-normalization"></a>變音符號正規化 

開啟變音符號對 LUIS JSON 的應用程式檔案中的 [utterance] 正規化`settings`參數。

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

下列的表達方式顯示變音符號正規化會如何影響談話：

|與變音符號設定為 false|變音符號設定為 true|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>變音符號的語言支援

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>巴西葡萄牙文`pt-br`變音符號

|變音符號設定為 false|變音符號設定為 false|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`| 
|||

#### <a name="dutch-nl-nl-diacritics"></a>荷蘭文`nl-nl`變音符號

|變音符號設定為 false|變音符號設定為 false|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`| 
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>法文`fr-`變音符號

這包括法文和加拿大子文化特性。

|變音符號設定為 false|變音符號設定為 false|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`| 
|`ê`|`e`| 
|`î`|`i`| 
|`ô`|`o`| 
|`û`|`u`| 
|`ç`|`c`| 
|`ë`|`e`| 
|`ï`|`i`| 
|`ü`|`u`| 
|`ÿ`|`y`| 

#### <a name="german-de-de-diacritics"></a>德文`de-de`變音符號

|變音符號設定為 false|變音符號設定為 false|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>義大利文`it-it`變音符號

|變音符號設定為 false|變音符號設定為 false|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó`|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>西班牙文`es-`變音符號

這包括西班牙文和加拿大墨西哥。

|變音符號設定為 false|變音符號設定為 false|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>標點符號正規化

開啟 [utterance] 正規化 LUIS JSON 的應用程式檔案中的標點符號`settings`參數。

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

下列的表達方式顯示變音符號有何影響的表達方式：

|與變音符號設定為 False|與變音符號設定為 True|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>已移除標點符號

已移除下列標點符號`NormalizePunctuation`設為 true。

|標點符號|
|--|
|`-`| 
|`.`| 
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
