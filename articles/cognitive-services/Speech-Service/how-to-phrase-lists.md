---
title: 片語清單-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何透過使用`PhraseListGrammar`物件的片語清單來提供語音服務, 以改善語音轉換文字辨識結果。
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562778"
---
# <a name="phrase-lists-for-speech-to-text"></a>語音轉換文字的片語清單

藉由提供語音服務與片語清單, 您可以改善語音辨識的精確度。 片語清單可用來識別音訊資料中的已知片語, 例如人員的姓名或特定位置。

例如, 如果您有一個命令「移至」, 而且可能會讀出「Ward」的目的地, 您可以新增「移至 Ward」的專案。 新增片語會增加可辨識「移至 Ward」的音訊, 而不是「移至」的可能性。

可以將單一單字或完整片語新增至片語清單。 辨識期間, 如果音訊中包含完全相符的專案, 則會使用片語清單中的專案。 以先前的範例為基礎, 如果片語清單包含「Move to Ward」, 而所捕捉的片語是「移至緩慢」, 則辨識結果會是「移至 Ward 緩慢」。

>[!Note]
> 目前, 片語清單僅支援英文的語音轉換文字。

## <a name="how-to-use-phrase-lists"></a>如何使用片語清單

下列範例說明如何使用`PhraseListGrammar`物件來建立片語清單。

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> 語音服務將用來比對語音的片語清單數目上限為1024個片語。

您也可以藉`PhraseListGrammar`由呼叫 clear (), 清除與相關聯的片語。

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> `PhraseListGrammar`對物件所做的變更會在下一次辨識或重新連線到語音服務之後生效。

## <a name="next-steps"></a>後續步驟

* [語音 SDK 參考檔](speech-sdk.md)
