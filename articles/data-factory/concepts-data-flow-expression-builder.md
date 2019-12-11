---
title: 對應資料流程中的運算式產生器
description: 使用運算式產生器在 Azure Data Factory 中對應資料流程的組建運算式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969331"
---
# <a name="building-expressions-in-mapping-data-flow"></a>在對應資料流程中建立運算式

在對應資料流程中，許多轉換屬性都是以運算式的形式輸入。 這些運算式是由資料行值、參數、函數、運算子，以及在執行時間評估為 spark 資料類型的常值所組成。

## <a name="opening-the-expression-builder"></a>開啟運算式產生器

Data factory UX 中的運算式編輯介面就是「**運算式**產生器」。 當您在運算式邏輯中輸入時，data factory 會使用[IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019)程式碼完成來進行反白顯示、語法檢查和自動完成。

![運算式產生器](media/data-flow/xpb1.png "運算式產生器")

在 [衍生的資料行和篩選] 之類的轉換（其中運算式是強制性的）中，按一下 [藍色運算式] 方塊來開啟 [運算式產生器]。

![運算式產生器](media/data-flow/expressionbox.png "運算式產生器")

當參考相符或 group by 條件中的資料行時，運算式可以從資料行中提取值。 若要建立運算式，請選取 [計算資料行] 選項。

![運算式產生器](media/data-flow/computedcolumn.png "運算式產生器")

在運算式或常值是有效輸入的情況下，「新增動態內容」可讓您建立評估為常值的運算式。

![運算式產生器](media/data-flow/add-dynamic-content.png "運算式產生器")

## <a name="expression-language-reference"></a>運算式語言參考

對應的資料流程具有內建函數和運算子，可用於運算式中。 您可以在[對應的資料流程運算式語言](data-flow-expression-functions.md)參考頁面中找到可用函式的清單。

## <a name="column-names-with-special-characters"></a>含特殊字元的資料行名稱

當您有包含特殊字元或空格的資料行名稱時，請用大括弧括住名稱，以在運算式中參考它們。

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>預覽運算式結果

如果已開啟 [[偵錯工具模式]](concepts-data-flow-debug-mode.md) ，您可以使用即時 spark 叢集查看運算式評估結果的進行中預覽。 當您建立邏輯時，您可以即時地對運算式進行偵錯工具。 

![運算式產生器](media/data-flow/exp4b.png "運算式資料預覽")

按一下 [重新整理] 按鈕，以針對您的來源即時取樣來更新運算式的結果。

![運算式產生器](media/data-flow/exp5.png "運算式資料預覽")

## <a name="string-interpolation"></a>字串插補

使用雙引號將常值字串文字與運算式一起括住。 您可以包含運算式函數、資料行和參數。 字串插補適用于在查詢字串中包含參數時，避免大量使用字串串連。 若要使用運算式語法，請將它括在大括弧中，

字串插補的一些範例：

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>批註運算式

使用單行或多行註解語法將註解新增到運算式中：

![註解](media/data-flow/comments.png "註解")

以下是有效批註的範例：

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

如果您將批註放在運算式的頂端，它就會出現在 [轉換] 文字方塊中，以記錄轉換運算式：

![註解](media/data-flow/comments2.png "註解")

## <a name="regular-expressions"></a>規則運算式

許多運算式語言函數使用正則運算式語法。 使用正則運算式函數時，運算式產生器會嘗試將反斜線（\\）解讀為 escape 字元序列。 在正則運算式中使用反斜線時，請以刻度（\`）括住整個 RegEx，或使用雙反斜線。

使用刻度的範例

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

或使用雙斜線

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>定址陣列索引

針對會傳回陣列的運算式函數，請使用方括號 [] 來處理內部會傳回陣列物件的特定索引。 陣列是以一為基礎。

![運算式產生器陣列](media/data-flow/expb2.png "運算式資料預覽")

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

* ```Ctrl-K Ctrl-C```：批註整行
* ```Ctrl-K Ctrl-U```：取消批註
* ```F1```：提供編輯器説明命令
* ```Alt-Down Arrow```：下移目前的行
* ```Alt-Up Arrow```：上移目前的行
* ```Cntrl-Space```：顯示內容說明

## <a name="convert-to-dates-or-timestamps"></a>轉換成日期或時間戳記

若要在時間戳記輸出中包含字串常值，您必須將轉換包裝在 ```toString()```中。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

若要將毫秒從 Epoch 轉換成日期或時間戳記，請使用 `toTimestamp(<number of milliseconds>)`。 如果時間是以秒為單位，則乘以1000。

```toTimestamp(1574127407*1000l)```

上述運算式結尾的尾端 "l" 表示轉換成長類型做為內嵌語法。

## <a name="next-steps"></a>後續步驟

[開始建立資料轉換運算式](data-flow-expression-functions.md)
