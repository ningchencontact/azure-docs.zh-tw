---
title: Azure Data Factory 對應資料流程中的聯結轉換 |Microsoft Docs
description: 使用 Azure Data Factory 對應資料流程中的聯結轉換，結合來自兩個數據源的資料
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 78de9f2bedfc36add567053e1de47e8893bfaf3c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597063"
---
# <a name="join-transformation-in-mapping-data-flow"></a>對應資料流程中的聯結轉換

使用聯結轉換，將兩個來源或資料流程中的資料結合在對應資料流程中。 輸出資料流程會包含兩個來源中的所有資料行，並根據聯結條件進行比對。 

## <a name="join-types"></a>聯結類型

對應的資料流程目前支援五種不同的聯結類型。

### <a name="inner-join"></a>內部聯結

內部聯結只會輸出具有相符值的兩個數據表的資料列。

### <a name="left-outer"></a>左方外部

左方外部聯結會從左邊的資料流程傳回所有資料列，並從右邊的資料流程傳回相符的記錄。 如果左側資料流程中的資料列沒有相符項，則右邊資料流程的輸出資料行會設定為 Null。 輸出將會是內部聯結所傳回的資料列，加上來自左側資料流程的不相符資料列。

### <a name="right-outer"></a>右方外部

左方外部聯結會從右邊的資料流程傳回所有資料列，並從左邊的資料流程傳回相符的記錄。 如果右邊資料流程中的資料列沒有相符項，則右邊資料流程的輸出資料行會設定為 Null。 輸出將會是內部聯結所傳回的資料列，再加上右邊資料流程中不相符的資料列。

### <a name="full-outer"></a>完整外部

完整外部聯結會從兩端輸出具有 Null 值的所有資料行和資料列，而不會相符。

### <a name="cross-join"></a>交叉聯結

交叉聯結會根據條件來輸出兩個數據流的交叉乘積。 如果您要使用不相等的條件，請指定自訂表格達式做為交叉聯結條件。 輸出資料流程將會是符合聯結條件的所有資料列。 若要建立輸出每個資料列組合的笛卡兒產品，請指定 `true()` 做為聯結條件。

## <a name="configuration"></a>組態

1. 在 [**右資料流程**] 下拉式清單中，選擇您要聯結的資料流程。
1. 選取您的**聯結類型**
1. 選擇您想要與之聯結條件相符的索引鍵資料行。 根據預設，資料流程會在每個資料流程中的一個資料行之間尋找是否相等。 若要透過計算值進行比較，請將滑鼠停留在資料行下拉式清單中，然後選取 [**計算資料行**]

![聯結轉換](media/data-flow/join.png "聯結")

## <a name="optimizing-join-performance"></a>優化聯結效能

不同于 SSIS 之類的工具中的合併聯結，聯結轉換不是強制合併聯結作業。 聯結索引鍵不需要排序。 聯結作業是根據 Spark 中的最佳聯結作業（廣播或對應端聯結）進行。

![聯結轉換優化](media/data-flow/joinoptimize.png "聯結優化")

如果其中一個或兩個數據流符合背景工作節點記憶體，請在 [優化] 索引標籤中啟用**廣播**，以進一步將效能優化。您也可以將聯結作業的資料重新分割，使其更適合每個背景工作的記憶體。

## <a name="self-join"></a>自我聯結

若要自我聯結資料流程本身，請使用 select 轉換來為現有的資料流程加上別名。 按一下轉換旁的加號圖示，然後選取 [**新增分支**]，以建立新的分支。 新增 [選取] 轉換，以將原始資料流程做為別名。 新增聯結轉換，並選擇原始資料流程作為**左邊**的資料流程，並選擇做為**右邊資料流程**的選取轉換。

![自我聯結](media/data-flow/selfjoin.png "自我聯結")

## <a name="testing-join-conditions"></a>測試聯結條件

在 debug 模式中使用資料預覽來測試聯結轉換時，請使用一小組已知的資料。 從大型資料集取樣資料列時，您無法預測要讀取哪些資料列和索引鍵進行測試。 結果是不具決定性的，這表示您的聯結條件可能不會傳回任何相符專案。

## <a name="data-flow-script"></a>資料流程腳本

### <a name="syntax"></a>語法

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>內部聯結範例

下列範例是名為 `JoinMatchedData` 的聯結轉換，它會將 `TripData` 和正確的資料流程 `TripFare`。  聯結條件是運算式 `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` 如果每個資料流程中的 `hack_license`、`medallion`、`vendor_id` 和 `pickup_datetime` 資料行相符，就會傳回 true。 @No__t_0 為 `'inner'`。 我們只會在左邊的串流中啟用廣播，因此 `broadcast` 的值 `'left'`。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![聯結範例](media/data-flow/join-script1.png "聯結範例")

此轉換的資料流程腳本位於下列程式碼片段中：

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="cross-join-example"></a>交叉聯結範例

下列範例是名為 `CartesianProduct` 的聯結轉換，它會將 `TripData` 和正確的資料流程 `TripFare`。 這項轉換會接受兩個數據流，並傳回其資料列的笛卡兒乘積。 聯結條件是 `true()` 的，因為它會輸出完整的笛卡兒乘積。 @No__t_1 中的 `joinType`。 我們只會在左邊的串流中啟用廣播，因此 `broadcast` 的值 `'left'`。

在 Data Factory UX 中，這項轉換看起來如下圖所示：

![聯結範例](media/data-flow/join-script2.png "聯結範例")

此轉換的資料流程腳本位於下列程式碼片段中：

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>後續步驟

聯結資料之後，請建立[衍生](data-flow-derived-column.md)的資料行，並將您的資料[接收](data-flow-sink.md)到目的地資料存放區。
