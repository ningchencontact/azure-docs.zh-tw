---
title: 人類看得標示為轉譯指導方針-語音服務
titlesuffix: Azure Cognitive Services
description: 如果您想要增進辨識準確度，特別是當刪除或不正確地用來替代字，因為所導致的問題您會想使用人類看得標示的轉譯，以及音訊資料。 人類看得標示為轉譯有哪些？ 很簡單，它們是依據英文單字，逐字則轉譯的音訊檔案。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7f0b467284872f3d936984741c6d092705008a5a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026728"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>如何建立可標示為轉譯

如果您想要增進辨識準確度，特別是當刪除或不正確地用來替代字，因為所導致的問題您會想使用人類看得標示的轉譯，以及音訊資料。 人類看得標示為轉譯有哪些？ 很簡單，它們是依據英文單字，逐字則轉譯的音訊檔案。

大型轉譯資料的範例，才可改善其辨識，建議您提供的轉譯資料的 10 到 1,000 小時之間。 在此頁面上，我們將檢閱指導方針旨在協助您建立高品質的轉譯。 本指南是依據地區設定，美式英文，普通話中文和德文的區段。

## <a name="us-english-en-us"></a>美式英文 (en-US)

必須提供英文音訊的人類看得標示為轉譯為純文字，只使用 ASCII 字元。 請避免使用拉丁文 1] 或 [Unicode 標點符號字元。 從文字處理應用程式或抓取資料，從網頁複製文字時，通常不小心會加入這些字元。 如果這些字元存在，請務必使用適當的 ASCII 替代來更新這些項目。

以下是一些範例：

| 要避免的字元 | 替代字元 | 注意 |
|---------------------|--------------|-------|
| “Hello world” | "Hello world" | 已有適當的 ASCII 字元取代開頭和結尾的引號。 |
| John’s day | John's day | 單引號已取代為適當的 ASCII 字元。 |
| it was good—no, it was great! | it was good--no, it was great! | Em dash 已取代兩個連字號。 |

### <a name="text-normalization-for-us-english"></a>適用於美國英文文字正規化

文字正規化是文字轉換成一致的格式，定型模型時使用。 某些正規化規則會自動套用至文字，不過，我們建議使用這些指導方針，因為準備人類看得標示的文字記錄資料：

* 寫出文字中的縮寫。
* 寫出在字詞 （例如會計條款） 中的非標準數值字串。
* 非字母字元或混合的英數字元應該謄寫，明顯。
* （例如"雷達圖"、"雷射"、"RAM"或"北大西洋公約組織 」），不應該編輯為單字都發音的縮寫。
* 寫出發音為個別的字母，以空格分隔每個字母的縮寫。

以下是正規化的一些您應該執行轉譯的範例：

| 原始文字 | 正規化後的文字 |
|---------------|--------------------------|
| Dr.Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| My blood type is O+ | My blood type is O positive |
| Water is H20 | Water is H 2 O |
| Play OU812 by Van Halen | Play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

下列的正規化規則會自動套用至轉譯：

* 使用小寫字母。
* 移除所有的標點符號，除了在單字內的單引號。
* 您可以展開 數字讀出單字/的格式，例如金額。

以下是正規化的幾個範例轉譯上自動執行：

| 原始文字 | 正規化後的文字 |
|---------------|--------------------------|
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
It costs $3.14| it costs three fourteen |

## <a name="mandarin-chinese-zh-cn"></a>國語中文 (zh-cn)

人類看得標示為轉譯普通話中文音訊必須是 utf-8 編碼的位元組順序標記。 請避免使用半形標點符號字元。 這些字元可以包含不小心準備文書處理程式中的資料，或擷取來自網頁的資料時。 如果這些字元存在，請務必使用適當的全形替代來更新這些項目。

以下是一些範例：

| 要避免的字元 | 替代字元 | 注意 |
|---------------------|--------------|-------|
| "你好" | "你好" | 已使用適當的字元取代開頭和結尾的引號。 |
| 需要什么帮助? | 需要什么帮助？ | 問號已取代為適當的字元。 |

### <a name="text-normalization-for-mandarin-chinese"></a>用於中文中文文字正規化

文字正規化是文字轉換成一致的格式，定型模型時使用。 某些正規化規則會自動套用至文字，不過，我們建議使用這些指導方針，因為準備人類看得標示的文字記錄資料：

* 寫出文字中的縮寫。
* 將數值字串以口語形式寫出。

以下是正規化的一些您應該執行轉譯的範例：

| 原始文字 | 正規化後的文字 |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

下列的正規化規則會自動套用至轉譯：

* 移除所有標點符號
* 展開以口語形式的數字
* 將全形字母轉換成半形字母
* 對於所有的英文字組使用大寫字母

以下是正規化的幾個範例轉譯上自動執行：

| 原始文字 | 正規化後的文字 |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>德文 (DE-DE) 和其他語言

人類看得標示的轉譯，德文的音訊 （和其他非英文或中文中文語言） 必須是 utf-8 編碼的位元組順序標記。 每個音訊檔案應該提供一個人類看得標示的文字記錄。

### <a name="text-normalization-for-german"></a>德文文字正規化

文字正規化是文字轉換成一致的格式，定型模型時使用。 某些正規化規則會自動套用至文字，不過，我們建議使用這些指導方針，因為準備人類看得標示的文字記錄資料：

*   撰寫小數位數為"，"而非"。"。
*   寫入做為時間分隔符號":"而非"。"(例如：12:00 Uhr)。
*   "ca." 之類的縮寫 不會被取代。 我們建議您使用完整的口語的形式。
*   已移除四個主要的數學運算子 (+、-、\* 和 /)。 我們建議取代的書寫形式為:"此外，""減，""惡意程式 」 和 「 geteilt。 」
*   會移除比較運算子 (=、 <、 和 >)。 建議並取代成 "gleich"、"kleiner als" 和 "grösser als"。
*   撰寫分數，3/4，例如以書面格式 (例如:"drei viertel"，而不是 3/4)。
*   "€"符號取代其書寫形式 「 歐洲 」。

以下是正規化的一些您應該執行轉譯的範例：

| 原始文字 | 使用者正規化之後的文字 | 之後系統正規化的文字 |
|---------------|-------------------------------|---------------------------------|
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

下列的正規化規則會自動套用至轉譯：

* 您可以使用小寫字母的所有文字。
* 移除所有標點符號，包括各種類型的引號 （"test"、 'test'、"test"和 «測試» 是 [確定]）。
* 捨棄資料列集中的任何特殊字元： 狀況 ¤ 是正確 」。 § © ª 您® ° 常见 ² µ × y Ø¬¬。
* 展開口語形式，包括美元或歐元數量的數字。
* 接受變音符號只會針對，o，而且您。 其他"th"會取代或捨棄。

以下是正規化的幾個範例轉譯上自動執行：

| 原始文字 | 正規化後的文字 |
|---------------|--------------------------|
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

## <a name="next-steps"></a>後續步驟

* [準備和測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [訓練您的模型](how-to-custom-speech-train-model.md)
* [將模型部署](how-to-custom-speech-deploy-model.md)
