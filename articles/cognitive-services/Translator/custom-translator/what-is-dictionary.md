---
title: 什麼是字典？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 字典是已對齊的文件，可指定您想要 Microsoft Translator 一律以相同方式翻譯的片語或句子清單。 字典有時也稱為字彙或詞彙。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5103526956b5041771a1d8e4abb5e8800b971059
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595380"
---
# <a name="what-is-a-dictionary"></a>什麼是字典？

字典是已對齊的一組文件，用來指定片語或句子及其對應之翻譯的清單。 在定型中使用字典，當您想要 Microsoft Translator 一律翻譯原始片語或句子的任何實例時，就會使用您在字典中提供的翻譯。 字典有時稱為字彙或詞彙。 您可以將字典視為針對您所列出的所有字詞，強制執行「複製並取代」。

字典僅適用於語言組中有完整支援之 Microsoft 類神經機器翻譯 (NMT) 系統作為後盾的專案。 請[參閱完整的語言清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)。

## <a name="phrase-dictionary"></a>片語字典
當您在定型模型中包含片語字典，則其中所列的任何單字或片語都會以您指定的方式翻譯。 句子的其餘部分會如往常般翻譯。 您可以使用片語字典來指定不應翻譯的片語，方法是在字典中的原始和目標檔案內提供相同的未翻譯片語。

## <a name="sentence-dictionary"></a>句子字典
句子字典可讓您針對原始句子指定精確的目標翻譯。 假設要比對句子字典，整個提交的句子就必須符合原始字典項目。  如果只有一部分的句子符合，則不會比對項目。  當偵測到比對時，就會傳回句子字典的目標項目。

## <a name="dictionary-only-trainings"></a>僅字典定型
您可以只使用字典資料來定型模型。 若要這麼做，請只選取您要包含的字典文件 (或多個字典文件)，並點選 [建立模型]。 由於這是僅字典定型，因此不需要最低數目的定型句子。 您的模型通常會比標準定型更快完成定型。  產生的模型將使用 Microsoft 翻譯基準模型加上您新增的字典來翻譯。  您不會取得測試報告。

>[!Note]
>自訂翻譯工具並不會將句子對齊字典檔案，因此在您的字典文件中務必要有相同數目的原始和目標片語/句子，如此才會精確對齊。

## <a name="recommendations"></a>建議

- 字典不是用來代替定型模型定型資料。  字典基本上會尋找並取代單字或句子。  比起使用字典，讓系統以完整句子的方式從定型材料學習是較佳的選擇。
- 您應該謹慎使用片語字典。 當句子中的片語被取代時，會遺失該句子內的上下文，或限制句子其餘部分的翻譯。 結果會導致即使句子中的片語或單字將依據片語字典翻譯，但句子整體的翻譯品質通常會受到影響。
- 片語字典非常適合用於複合名詞，例如產品名稱 (“Microsoft SQL Server”)、專有名稱 (「漢堡市」)，或是產品功能 (「樞紐分析表」)。 它並不適合用於動詞或形容詞，因為這些內容通常會受到原始語言或目標語言的高度影響。 請避免對複合名詞以外的任何內容使用片語字典項目。
- 當您使用字典時，翻譯中提供的大小寫和標點符號會影響目標檔案中提供的大小寫和標點符號。 在字典檔案中嘗試找出輸入句子和原始句子間的相符項目時，會忽略大小寫和標點符號。 例如，假設我們定型了某個英文翻譯成西班牙文的系統，其中使用的字典在原始檔案中指定 “City of Hamburg”，並在目標檔案中指定 “Ciudad de hamburg”。 如果要求的句子翻譯包含片語 “city of Hamburg”，則 “city of Hamburg” 符合字典檔案中的項目 “City of Hamburg”，並且會在最終翻譯中對應至 “Ciudad de hamburg”。
- 如果單字在字典檔案中出現一次以上，系統將一律使用最後提供的項目。 您的字典不應包含相同單字的多個翻譯。

## <a name="next-steps"></a>後續步驟

- 了解[文件格式的指導方針](document-formats-naming-convention.md)。
