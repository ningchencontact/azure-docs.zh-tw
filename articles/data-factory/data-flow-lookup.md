---
title: Azure Data Factory 對應資料流程查閱轉換
description: Azure Data Factory 對應資料流程查閱轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787998"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory 對應資料流程查閱轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用查閱以將另一個來源的參考資料新增至資料流程。 查閱轉換需要定義的來源，其指向您的參考資料表且會比對索引鍵欄位。

![查閱轉換](media/data-flow/lookup1.png "查閱")

選取您想要在內送資料流欄位與參考來源欄位之間比對的索引鍵欄位。 您必須先在資料流程設計畫布上建立新的來源，以作為查閱的右端。

當找到相符項目時，來自參考來源的結果資料列和資料行將會新增到資料流程中。 您可以選擇有興趣且希望包含在資料流結尾接收 (Sink) 中的欄位。

## <a name="optimizations"></a>最佳化

Data Factory 中的資料流程在相應放大 Spark 環境中執行。 如果您的資料集可以放入背景工作節點的記憶體空間中，我們可以最佳化您查閱的效能。

![廣播聯結](media/data-flow/broadcast.png "廣播聯結")

### <a name="broadcast-join"></a>廣播的聯結

選取左和/或右側廣播要求，將從任一端的查閱關聯性的整個資料集推送記憶體的 ADF 的聯結。

### <a name="data-partitioning"></a>資料分割

您也可以指定所選取 「 設定分割 」 最佳化 索引標籤上的 「 查閱 」 轉換來建立於每個背景工作的記憶體更能容納的資料集的資料分割的資料。

## <a name="next-steps"></a>後續步驟

[聯結](data-flow-join.md)並[Exists](data-flow-exists.md)轉換 ADF 對應資料流程中執行類似的工作。 看看這些轉換下一步。
