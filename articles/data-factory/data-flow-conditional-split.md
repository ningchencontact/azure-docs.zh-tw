---
title: Azure Data Factory 對應的資料流程條件式分割轉換
description: Azure Data Factory 資料流程條件式分割轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d06b5b86737d0940930a3ccea3b6d65be0a802f9
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387894"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>對應資料流程條件式分割轉換



![條件式分割工具箱](media/data-flow/conditionalsplit2.png "條件式分割工具箱")

條件式分割轉換可以視資料內容而定，將資料列路由傳送到不同的資料流。 條件式分割轉換的實作類似程式設計語言中的 CASE 決策結構。 轉換會評估運算式，並根據結果將資料列導向特定資料流。 此轉換也提供預設輸出，因此若某個資料列不符合任何運算式，它會被導向到預設輸出。

![條件式分割](media/data-flow/conditionalsplit1.png "條件式分割選項")

## <a name="multiple-paths"></a>多個路徑

若要新增額外的條件，請選取設定窗格底部的 [加入資料流]，然後按一下 [運算式產生器] 文字方塊以建置您的運算式。

![條件式分割多個](media/data-flow/conditionalsplit3.png "條件式分割多個")

## <a name="next-steps"></a>後續步驟

搭配條件式分割使用的一般資料流程轉換：[聯結轉換](data-flow-join.md)、[查閱轉換](data-flow-lookup.md)、[選取轉換](data-flow-select.md)
