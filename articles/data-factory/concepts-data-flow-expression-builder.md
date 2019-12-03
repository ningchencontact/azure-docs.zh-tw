---
title: Azure Data Factory 對應資料流程運算式產生器
description: Azure Data Factory 對應資料流程的運算式產生器
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 0eb2c2692ed2444a85e7253c6fdd8734385ff881
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672257"
---
# <a name="mapping-data-flow-expression-builder"></a>對應資料流程運算式產生器



在 Azure Data Factory 的對應資料流程中，您會找到運算式方塊，您可以在其中輸入運算式來進行資料轉換。 請在這些方塊中使用來自您資料流程的資料行、欄位、變數、參數、函數。 若要建置運算式，請使用運算式產生器，其可透過按一下轉換內的運算式文字方塊來啟動。 針對轉換選取資料行時，您有時候也會看見 [計算資料行] 選項。 如果您按一下它，便會啟動運算式產生器。

![運算式產生器](media/data-flow/xpb1.png "運算式產生器")

運算式產生器工具的預設選項是文字編輯器。 自動完成功能會從整個 Azure Data Factory 資料流程物件模型進行讀取，並搭配語法檢查和醒目提示功能。

![運算式產生器自動完成](media/data-flow/expb1.png "運算式產生器自動完成")

## <a name="build-schemas-in-output-schema-pane"></a>在輸出架構窗格中建立架構

![加入複雜資料行](media/data-flow/complexcolumn.png "新增資料行")

在左側的 [輸出架構] 窗格中，您會看到您要修改的資料行，並將其新增至您的架構。 您可以在這裡以互動方式建立簡單和複雜的資料結構。 使用「新增資料行」來新增其他欄位，並使用「新增 subcolumn」來建立階層。

![新增 subcolumn](media/data-flow/addsubcolumn.png "新增 Subcolumn")

## <a name="data-preview-in-debug-mode"></a>在 debug 模式中的資料預覽

![運算式產生器](media/data-flow/exp4b.png "運算式資料預覽")

當您處理資料流程運算式時，請從 Azure Data Factory 的資料流程設計介面切換到 [Debug] 模式，從您所建立的運算式，啟用資料結果的即時進行中預覽。 已針對您的運算式啟用即時偵錯。

![Debug 模式](media/data-flow/debugbutton.png "[調試] 按鈕")

按一下 [重新整理] 按鈕，即可即時更新您的來源即時範例的運算式結果。

![運算式產生器](media/data-flow/exp5.png "運算式資料預覽")

## <a name="comments"></a>註解

使用單行或多行註解語法將註解新增到運算式中：

![註解](media/data-flow/comments.png "註解")

## <a name="regular-expressions"></a>規則運算式

Azure Data Factory 資料流程運算式語言 ([完整參考文件在此](https://aka.ms/dataflowexpressions) \(英文\)) 可提供包含規則運算式語法的函數。 使用正則運算式函數時，運算式產生器會嘗試將反斜線（\\）解讀為 escape 字元序列。 在正則運算式中使用反斜線時，請以刻度（\`）括住整個 RegEx，或使用雙反斜線。

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

## <a name="handling-names-with-special-characters"></a>處理具有特殊字元的名稱

當您有包含特殊字元或空格的資料行名稱時，請以大括弧括住名稱。
* ```{[dbo].this_is my complex name$$$}```

## <a name="keyboard-shortcuts"></a>鍵盤快速鍵

* ```Ctrl-K Ctrl-C```：批註整行
* ```Ctrl-K Ctrl-U```：取消批註
* ```F1```：提供編輯器説明命令
* ```Alt-Down Arrow```：下移目前的行
* ```Alt-Up Arrow```：向上移動目前的一行
* ```Cntrl-Space```：顯示內容說明

## <a name="manual-comments"></a>手動留言

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

如果您將批註放在運算式的頂端，它就會出現在 [轉換] 文字方塊中，以記錄轉換運算式：

![註解](media/data-flow/comments2.png "註解")

## <a name="convert-to-dates-or-timestamps"></a>轉換成日期或時間戳記

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

請注意，若要在時間戳記輸出中包含字串常值，您必須將轉換包裝在 ```toString()```內。

以下說明如何將秒數從 Epoch 轉換成日期或時間戳記：

```toTimestamp(1574127407*1000l)```

請注意上述運算式結尾處的尾端 "l"。 這表示轉換成長期的語法。

## <a name="handling-column-names-with-special-characters"></a>處理含有特殊字元的資料行名稱

當您有包含特殊字元或空格的資料行名稱時，請以大括弧括住名稱。

```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>後續步驟

[開始建立資料轉換運算式](data-flow-expression-functions.md)
