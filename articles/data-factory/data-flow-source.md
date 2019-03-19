---
title: Azure Data Factory 對應資料流程來源轉換
description: Azure Data Factory 對應資料流程來源轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 20491981cb02e428ff4114b9456d74b0de651be8
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569020"
---
# <a name="mapping-data-flow-source-transformation"></a>對應資料流程來源轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

來源轉換會設定您想要用來將資料帶入資料流程的資料來源。 在單一資料流程中，您可以有多個來源轉換。 一定會開始設計您的資料流動，以來源轉換。

> [!NOTE]
> 每個資料流程需要至少一個來源轉換。 視需要新增多個其他來源，以完成您的資料轉換。 您可以使用聯結或聯集轉換，將這些來源聯結在一起。 當偵錯在偵錯工作階段中的資料流時，則會使用取樣設定或偵錯來源限制從來源讀取資料。 不過，不會再寫入資料到接收在您執行在資料流程管線資料流活動之前。 

![來源轉換選項](media/data-flow/source.png "來源")

每個資料流程來源轉換必須只有一個 Data Factory 資料集相關聯。 資料集定義的圖形和寫入或讀取資料的位置。 您也可以在您的來源中使用萬用字元和檔案的清單，一次處理一個以上的檔案時使用檔案的來源。

## <a name="data-flow-staging-areas"></a>資料流程暫存區域

資料流程會處理 Azure 中所有的「預備」資料集。 這些資料流程資料集用於預備資料，以執行您的資料轉換。 Data Factory 可存取將近 80 個不同的原生連接器。 若要將其他來源的資料包含在您的資料流程中，請使用複製活動先將該資料暫存在其中一個資料流程資料集暫存區域中。

## <a name="options"></a>選項

### <a name="allow-schema-drift"></a>允許結構描述漂移
如果來源資料行會經常變更，請選取 [允許結構描述漂移]。 此設定將允許從您的來源傳入的所有欄位將轉換送至接收。

### <a name="validate-schema"></a>驗證結構描述

![公用來源](media/data-flow/source1.png "公用來源 1")

如果來源資料的新近版本不符合定義的結構描述，則資料流程會執行失敗。

### <a name="sampling"></a>取樣
使用取樣來限制您來源中的資料列數目。  這是測試或從您的來源，以進行偵錯取樣資料時很有用。

## <a name="define-schema"></a>定義結構描述

![來源轉換](media/data-flow/source2.png "來源 2")

對於不是強型別的來源檔案類型 (也就是相對於 Parquet 檔案的一般檔案)，您應該在來源轉換中定義每個欄位的資料類型。 您可以接著在選取轉換中變更資料行名稱，以及在衍生資料行轉換中變更資料類型。 

![來源轉換](media/data-flow/source003.png "資料類型")

針對強型別來源，您可以修改中後續的選取轉換的資料類型。 

### <a name="optimize"></a>最佳化

![來源資料分割](media/data-flow/sourcepart.png "資料分割")

在來源轉換的 [最佳化] 索引標籤上，您會看到稱為 [來源] 的額外資料分割類型。 這會只在您選取 Azure SQL DB 作為來源時有作用。 因為 ADF 想要平行處理連線，以便對您的 Azure SQL DB 來源執行大型查詢。

在您的 SQL DB 來源上分割資料是選擇性作業，但是對於大型查詢很有用。 您有兩個選擇：

### <a name="column"></a>欄

從您的來源資料表中選取要分割的資料行。 您也必須設定連線數目上限。

### <a name="query-condition"></a>查詢條件

您可以選擇根據查詢來分割連線。 針對這個選項，只要放入 WHERE 述詞的內容即可。 也就是年 > 1980

## <a name="source-file-management"></a>來源檔案管理
![新增來源設定](media/data-flow/source2.png "新增設定")

* 萬用字元路徑，從您的來源資料夾中挑選一系列符合模式的檔案。 這會覆寫您已設定您的資料集定義中的任何檔案。
* 檔案清單。 與檔案集相同。 指向您建立的文字檔案，其中包含一份要處理的相對路徑檔案清單。
* 要儲存檔案名稱的資料行會在您資料的資料行中，儲存來源中的檔案名稱。 在此輸入新的名稱以儲存檔案名稱字串。
* 完成之後 (在資料流執行之後，您可以選擇不對來源檔案執行任何作業、刪除來源檔案或移動來源檔案。) 用於移動的路徑都是相對路徑。

### <a name="sql-datasets"></a>SQL 資料集

當您使用 Azure SQL DB 或 Azure SQL DW 作為來源時，您會有其他選項。

* 查詢：對您的來源輸入 SQL 查詢。 設定查詢將會覆寫您在資料集中選擇的任何資料表。 請注意，這裡不支援 Order By 子句。

* 批次大小：輸入批次大小，以將大量資料分成批次大小的讀取。

> [!NOTE]
> 只有在管線中使用「執行資料流程」活動，從管線回合 (管線偵錯或執行回合) 執行資料流程時，才會執行檔案作業設定。 檔案作業不會在資料流程偵錯模式中執行。

### <a name="projection"></a>投射

![投射](media/data-flow/source3.png "投射")

類似於資料集中的結構描述，來源中的投射可定義資料的資料行、資料類型，以及來源資料中的資料格式。 如果您有未定義結構描述的文字檔案，請按一下 [偵測資料類型] 以要求 ADF 嘗試取樣並推斷資料類型。 您可以使用 [定義預設格式] 按鈕，設定自動偵測的預設資料格式。 您可以修改後續衍生資料行轉換中的資料行資料類型。 使用選取轉換可以修改資料行名稱。

![預設格式](media/data-flow/source2.png "預設格式")

## <a name="next-steps"></a>後續步驟

開始使用[衍生資料行](data-flow-derived-column.md)和[選取](data-flow-select.md)建置您的資料轉換。
