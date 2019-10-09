---
title: Azure Data Factory 對應資料流程查閱轉換
description: Azure Data Factory 對應資料流程查閱轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029336"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory 對應資料流程查閱轉換



使用查閱以將另一個來源的參考資料新增至資料流程。 查閱轉換需要定義的來源，其指向您的參考資料表且會比對索引鍵欄位。

![查閱轉換](media/data-flow/lookup1.png "查閱")

選取您想要在內送資料流欄位與參考來源欄位之間比對的索引鍵欄位。 您必須先在資料流程設計畫布上建立新的來源，以作為查閱的右端。

當找到相符項目時，來自參考來源的結果資料列和資料行將會新增到資料流程中。 您可以選擇有興趣且希望包含在資料流結尾接收 (Sink) 中的欄位。

## <a name="match--no-match"></a>符合/不符合

在您的查閱轉換之後，您可以使用運算式函數 `isMatch()`，利用後續轉換來檢查每個相符資料列的結果，以根據查閱是否導致資料列相符而在邏輯中進行進一步的選擇。

## <a name="optimizations"></a>最佳化

在 Data Factory 中，資料流程會在相應放大的 Spark 環境中執行。 如果您的資料集可以放入背景工作節點記憶體空間，我們可以優化您的查閱效能。

![廣播聯結](media/data-flow/broadcast.png "廣播聯結")

### <a name="broadcast-join"></a>廣播聯結

選取 [左側和/或右側廣播聯結]，要求 ADF 將整個資料集從查閱關聯性的任一邊推送至記憶體中。

### <a name="data-partitioning"></a>資料分割

您也可以在「查閱」轉換的 [優化] 索引標籤上選取 [設定分割]，以指定資料的分割，以建立可在每個背景工作的記憶體中容納較佳的資料集。

## <a name="next-steps"></a>後續步驟

[聯結](data-flow-join.md)和[存在](data-flow-exists.md)轉換會在 ADF 對應資料流程中執行類似的工作。 接下來請看一下這些轉換。
