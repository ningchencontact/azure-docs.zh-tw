---
title: 片語清單-語音服務
titlesuffix: Azure Cognitive Services
description: 了解如何提供語音服務，片語清單，使用`PhraseListGrammar`以改善語音轉換文字辨識結果的物件。
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: a3be5d28ebe394771a2d8b492f1f6a9c8a82fb9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66515313"
---
# <a name="phrase-lists-for-speech-to-text"></a>語音轉換文字的片語清單

藉由提供語音服務，片語清單，您可以改善語音辨識的精確度。 片語清單用來識別已知的片語，音訊資料，例如連絡人的名稱或特定位置中。

例如，如果您有一個命令 「 移至 」 和可能的目的地的 「 Ward 」 可能會說出，您可以新增 「 移動 Ward"的項目。 將片語會增加機率，當 「 移動 Ward 」 也會辨識而不是"進展到 「 辨識音訊。

單字或完整片語可以新增至片語清單。 辨識期間如果完全符合包含音訊，會使用片語清單中的項目。 如果此片語清單包含"移動至 Ward 」，以及片語擷取，在上述範例中，建置 」 移到緩慢 」，則辨識結果會是 「 緩時變移至 Ward 」。

## <a name="how-to-use-phrase-lists"></a>如何使用片語清單

下列範例說明如何建置片語清單，使用`PhraseListGrammar`物件。

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
> 語音服務會使用來比對語音片語清單的最大數目是 1024年片語。

您也可以清除相關聯的片語`PhraseListGrammar`藉由呼叫 clear （）。

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
> 若要變更`PhraseListGrammar`物件的下一步 辨識，或重新連線至語音服務之後生效。

## <a name="next-steps"></a>後續步驟

* [語音 SDK 參考文件](speech-sdk.md)
