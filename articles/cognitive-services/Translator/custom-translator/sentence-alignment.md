---
title: 句子配對與對齊 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 在執行訓練時，出現在平行文件中的句子皆已配對或已對齊。 自訂翻譯工具會藉由讀取句子和此句子的翻譯，一次一個句子地學習翻譯。 接著將這兩個句子中的字組和片語彼此對齊。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: adbc21c3e963a98a8482de0c26bf5e257f43013e
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675459"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>平行文件中的句子配對與對齊

在訓練時，出現在平行文件中的句子皆已配對或已對齊。 自訂翻譯工具會報告其可以在每個資料集中配對為「對齊句子」的數目。

## <a name="pairing-and-alignment-process"></a>配對和對齊程序

自訂翻譯工具會一次一個句子地學習句子翻譯。 它會從來源中讀取句子，然後從目標中讀取這個句子的翻譯。 接著將這兩個句子中的字組和片語彼此對齊。 此程序可讓自訂翻譯工具為一個句子中的字組和片語，以及此句子翻譯中的同等字組和片語建立對應。 對齊功能會嘗試確保系統是在彼此的翻譯句子上進行訓練。

## <a name="pre-aligned-documents"></a>預先對齊的文件

如果您知道您有平行文件，您可能會提供預先對齊的文字檔案來覆寫句子對齊。 您可以將這兩份文件中的所有句子擷取到文字檔，並組織成每一行一個句子，然後以 `.align` 副檔名來上傳檔案。 `.align` 副檔名會告知自訂翻譯工具應該略過句子對齊。

為了獲得最佳結果，請嘗試確定您的檔案內容是每行一個句子。 句子中沒有分行符號，因為這會造成不佳的對齊。

## <a name="suggested-minimum-number-of-sentences"></a>建議的最小句子數目

為了讓定型成功，下表顯示每種檔案類型中所需的最小句子數目。 這項限制是安全的網路，可確保您的平行句子包含足夠的唯一詞彙，以成功訓練翻譯模型。 一般的指導方針是擁有更多的人力轉譯品質的跨領域平行句子，應該會產生高品質的模型。

| 文件類型   | 建議的最小句子計數 | 最大句子計數 |
|------------|--------------------------------------------|--------------------------------|
| 訓練   | 10,000                                     | 沒有上限                 |
| 調整     | 5,000                                      | 2,500                          |
| 測試    | 5,000                                      | 2,500                          |
| 字典 | 0                                          | 沒有上限                 |

> [!NOTE]
> - 如果不符合定型的10000最小句子計數，訓練將不會啟動，且將會失敗。 
> - 微調和測試是選擇性的。 如果您未提供這些值，系統將會移除定型的適當百分比，以用於驗證和測試。 
> - 您可以只使用字典資料來定型模型。 請參閱[什麼是字典](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary)。

## <a name="next-steps"></a>後續步驟

- 了解如何在自訂翻譯工具中使用[字典](what-is-dictionary.md)。
