---
title: Azure Data Factory 對應資料流程運算式產生器
description: 適用於 Azure Data Factory 應資料流程的運算式產生器
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: df9cfb0c0e36f54c8b1fbee4def552c78e9d42c1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359783"
---
# <a name="mapping-data-flow-expression-builder"></a>對應資料流程運算式產生器

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

在 Azure Data Factory 對應資料流程中，您將能找到可針對資料轉換輸入運算式的運算式方塊。 請在這些方塊中使用來自您資料流程的資料行、欄位、變數、參數、函數。 若要建置運算式，請使用運算式產生器，其可透過按一下轉換內的運算式文字方塊來啟動。 針對轉換選取資料行時，您有時候也會看見 [計算資料行] 選項。 如果您按一下它，便會啟動運算式產生器。

![運算式產生器](media/data-flow/expression.png "運算式產生器")

運算式產生器工具的預設選項是文字編輯器。 自動完成功能會從整個 Azure Data Factory 資料流程物件模型進行讀取，並搭配語法檢查和醒目提示功能。

![運算式產生器自動完成](media/data-flow/expb1.png "運算式產生器自動完成")

## <a name="currently-working-on-field"></a>[目前正在處理] 欄位

![運算式產生器](media/data-flow/exp3.png "目前正在處理")

在運算式產生器 UI 的左上角，您將會看到一個稱為 [目前正在處理] 的欄位，其具有您目前正在處理的欄位名稱。 您在 UI 中建置的運算式只會被套用到目前正在處理的欄位上。 如果您想要轉換另一個欄位，請儲存目前的工作，並使用此下拉式清單來選取另一個欄位，然後針對其他欄位建置運算式。

## <a name="data-preview-in-debug-mode"></a>偵錯模式中的資料預覽

![運算式產生器](media/data-flow/exp4b.png "運算式 [資料預覽]")

當您在處理運算式時，您可以選擇性地從 Azure Data Factory 資料流程設計界面開啟偵錯模式，以取得來自您正在建置之運算式的資料結果即時進行中預覽。 已針對您的運算式啟用即時偵錯。

![偵錯模式](media/data-flow/debugbutton.png "[偵錯] 按鈕")


![運算式產生器](media/data-flow/exp5.png "運算式 [資料預覽]")

## <a name="comments"></a>註解

使用單行或多行註解語法將註解新增到運算式中：

![註解](media/data-flow/comments.png "註解")

## <a name="regular-expressions"></a>規則運算式

Azure Data Factory 資料流程運算式語言 ([完整參考文件在此](https://aka.ms/dataflowexpressions) \(英文\)) 可提供包含規則運算式語法的函數。 使用規則運算式函數時，運算式產生器會嘗試解譯反斜線 (\\) 為逸出字元序列。 當您的規則運算式中使用反斜線，其中一個括住整個 regex 刻度 (\`) 或使用雙反斜線。

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

![運算式產生器陣列](media/data-flow/expb2.png "運算式 [資料預覽]")

## <a name="handling-names-with-special-characters"></a>處理特殊字元的名稱

當您有資料行名稱包含特殊字元或空格時，括住的大括號名稱。
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>後續步驟

[開始建立資料轉換運算式](data-flow-expression-functions.md)
