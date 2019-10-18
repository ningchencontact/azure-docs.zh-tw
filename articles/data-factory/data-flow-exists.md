---
title: Azure Data Factory 對應資料流程中的 Exists 轉換 |Microsoft Docs
description: 使用 Azure Data Factory 對應資料流程中的 exists 轉換來檢查現有的資料列
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: dfd304b0c15b325208daba104bb79863fcd3f53f
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527439"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>對應資料流程中的 Exists 轉換

Exists 轉換是一個資料列篩選轉換，它會檢查您的資料是否存在於另一個來源或資料流程中。 輸出資料流程包含左側資料流程中的所有資料列，其存在或不存在於正確的資料流程中。 Exists 轉換類似于 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS```。

## <a name="configuration"></a>組態

在 [**正確資料流程**] 下拉式清單中，選擇您要檢查哪一個資料流程是否存在。

指定您要尋找的資料是否存在，或不存在於 [**存在類型**] 設定中。

選擇您想要比較哪一個索引鍵資料行做為現有的條件。 根據預設，資料流程會在每個資料流程中的一個資料行之間尋找是否相等。 若要透過計算值進行比較，請將滑鼠停留在資料行下拉式清單，然後選取 [**計算資料行**]。

![存在設定](media/data-flow/exists.png "存在1")

### <a name="multiple-exists-conditions"></a>多個存在條件

若要比較每個資料流程中的多個資料行，請按一下現有資料列旁的加號圖示，以加入新的 exists 條件。 每個額外的條件都是由 "and" 語句聯結。 比較兩個數據行與下列運算式相同：

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>自訂表格達式

若要建立包含「and」和「equals to」以外之運算子的自由形式運算式，請選取 [**自訂表格達式**] 欄位。 在 [資料流程運算式產生器] 中按一下藍色方塊，以輸入自訂表格達式。

![已存在自訂設定](media/data-flow/exists1.png "存在自訂")

## <a name="data-flow-script"></a>資料流程腳本

### <a name="syntax"></a>語法

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>範例

下列範例是名為 `checkForChanges` 的 exists 轉換，它會將左資料流程 `NameNorm2` 和正確的資料流程 `TypeConversions`。  Exists 條件是運算式 `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` 如果每個資料流程中的 `EMPID` 和 `Region` 資料行都相符，就會傳回 true。 因為我們要檢查是否存在，`negate` 為 false。 我們不會在 [優化] 索引標籤中啟用任何廣播，因此 `broadcast` 具有 `'none'` 的值。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![Exists 範例](media/data-flow/exists-script.png "Exists 範例")

此轉換的資料流程腳本位於下列程式碼片段中：

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>後續步驟

類似的轉換是[查閱](data-flow-lookup.md)和[聯結](data-flow-join.md)。
