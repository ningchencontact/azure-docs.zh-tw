---
title: Azure Data Factory 對應資料流程查閱轉換
description: Azure Data Factory 對應資料流程查閱轉換
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: d762bddbe098e30cbf9e9c02da3c06073a358b12
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249242"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory 對應資料流程查閱轉換

使用查閱以將另一個來源的參考資料新增至資料流程。 查閱轉換需要定義的來源，其指向您的參考資料表且會比對索引鍵欄位。

![查閱轉換](media/data-flow/lookup1.png "查閱")

選取您想要在內送資料流欄位與參考來源欄位之間比對的索引鍵欄位。 您必須先在資料流程設計畫布上建立新的來源，以作為查閱的右端。

當找到相符項目時，來自參考來源的結果資料列和資料行將會新增到資料流程中。 您可以選擇有興趣且希望包含在資料流結尾接收 (Sink) 中的欄位。 或者，在您的查閱之後使用 Select 轉換來剪除欄位清單，只保留您想要保留的兩個數據流中的欄位。

「查閱」轉換會執行左方外部聯結的對等。 因此，您會看到左側來源中的所有資料列都與右側的相符專案結合。 如果您的查閱中有多個相符的值，或如果您想要自訂查閱運算式，則最好切換至聯結轉換，並使用交叉聯結。 這可避免執行任何可能的笛卡兒產品錯誤。

## <a name="match--no-match"></a>符合/不符合

在您的查閱轉換之後，您可以使用運算式函數 `isMatch()`，利用後續轉換來檢查每個相符資料列的結果，以根據查閱是否導致資料列相符而在邏輯中進行進一步的選擇。

## <a name="optimizations"></a>最佳化

在 Data Factory 中，資料流程會在相應放大的 Spark 環境中執行。 如果您的資料集可以放入背景工作節點記憶體空間，我們可以優化您的查閱效能。

![廣播聯結](media/data-flow/broadcast.png "廣播聯結")

### <a name="broadcast-join"></a>廣播聯結

選取 [左側和/或右側廣播聯結]，要求 ADF 將整個資料集從查閱關聯性的任一邊推送至記憶體中。 對於較小的資料集，這可大幅改善您的查閱效能。

### <a name="data-partitioning"></a>資料分割

您也可以在「查閱」轉換的 [優化] 索引標籤上選取 [設定分割]，以指定資料的分割，以建立可在每個背景工作的記憶體中容納較佳的資料集。

## <a name="next-steps"></a>後續步驟

[聯結](data-flow-join.md)和[存在](data-flow-exists.md)轉換會在 ADF 對應資料流程中執行類似的工作。 接下來請看一下這些轉換。
