---
title: 句子配對與對齊 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 在執行訓練時，出現在平行文件中的句子皆已配對或已對齊。 自訂翻譯工具會藉由讀取句子和此句子的翻譯，一次一個句子地學習翻譯。 然後將這兩個句子中的字組和片語彼此對齊。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595467"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>平行文件中的句子配對與對齊

在訓練時，出現在平行文件中的句子皆已配對或已對齊。 自訂翻譯工具會報告其可以在每個資料集中配對為「對齊句子」的數目。

## <a name="pairing-and-alignment-process"></a>配對和對齊程序

自訂翻譯工具會一次一個句子地學習句子翻譯。 它會從來源中讀取句子，然後從目標中讀取這個句子的翻譯。 接著將這兩個句子中的字組和片語彼此對齊。 此程序可讓自訂翻譯工具為一個句子中的字組和片語，以及此句子翻譯中的同等字組和片語建立對應。 對齊功能會嘗試確保系統是在彼此的翻譯句子上進行訓練。

## <a name="pre-aligned-documents"></a>預先對齊的文件

如果您知道您有平行文件，您可能會提供預先對齊的文字檔案來覆寫句子對齊。 您可以將這兩份文件中的所有句子擷取到文字檔，並組織成每一行一個句子，然後以 `.align` 副檔名來上傳檔案。 `.align` 副檔名會告知自訂翻譯工具應該略過句子對齊。

為了獲得最佳結果，請嘗試確定您的檔案內容是每行一個句子。 一個句子中不能有換行字元，因為這會導致不好的對齊效果。

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>擷取與對齊句子的最小數目建議

為了讓訓練能成功，下表將說明每個資料集裡最少需要多少個擷取句子和對齊句子。 擷取句子的最小數目建議會遠多於對齊句子的最小數目建議，這是考慮到句子對齊功能可能無法成功對齊所有擷取的句子。

| 資料集   | 擷取句子的最小數量建議 | 對齊句子的最小數量建議 | 對齊句子的最大數量 |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| 訓練   | 10,000                                     | 2,000                                    | 沒有上限                 |
| 調整     | 2,000                                      | 500                                      | 2,500                          |
| 測試    | 2,000                                      | 500                                      | 2,500                          |
| 字典 | 0                                          | 0                                        | 沒有上限                 |

## <a name="next-steps"></a>後續步驟

- 了解如何在自訂翻譯工具中使用[字典](what-is-dictionary.md)。
