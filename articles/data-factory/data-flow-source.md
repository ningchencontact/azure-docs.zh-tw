---
title: 設定 Azure Data Factory 中的對應 Data Flow 功能的來源轉換
description: 了解如何設定來源中的轉換對應資料流程。
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 5b53819c1d30f6cd62c5941d4b44d70a4996daad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67117887"
---
# <a name="source-transformation-for-mapping-data-flow"></a>適用於對應資料流程的來源轉換 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

「 來源 」 轉換會設定您的資料來源的資料流程。 資料流程可以包含多個來源轉換。 當設計資料流動時，一律以 來源轉換開頭。

每個資料流程會需要至少一個來源轉換。 若要完成您的資料轉換中，視需要新增多個來源。 您可以加入這些來源和 「 聯結 」 轉換或等位的轉換。

> [!NOTE]
> 當您偵錯資料流時，會讀取資料來源的資料所使用的取樣設定] 或 [偵錯來源限制。 若要將資料寫入至接收，您必須從資料流活動的管線執行資料流程。 

![來源 [來源設定] 索引標籤上的轉換選項](media/data-flow/source.png "來源")

只有一個 Data Factory 資料集相關聯程式資料流程來源轉換。 資料集定義的圖形和您想要寫入或讀取的資料的位置。 您可以使用您在來源中的萬用字元和檔案的清單，才能使用多個檔案一次。

## <a name="data-flow-staging-areas"></a>資料流臨時區域

適用於資料流程*預備*所有在 Azure 中的資料集。 臨時區域，您要轉換您的資料時使用這些資料集。 

Data Factory 有將近 80 原生的連接器的存取。 若要在資料流程中加入這些其他來源的資料，使用 「 複製活動 」 工具來接移一個資料流程的資料集臨時區域中的資料。

## <a name="options"></a>選項

選擇結構描述和取樣選項，為您的資料。

### <a name="allow-schema-drift"></a>允許結構描述漂移
選取 **允許結構描述漂移**如果會經常變更的來源資料行。 此設定可讓所有連入的流量通過接收轉換的來源欄位。

### <a name="validate-schema"></a>驗證結構描述

如果來源資料的內送的版本不符合定義的結構描述，將無法執行資料流程的資料。

![公用來源的設定，顯示驗證結構描述，允許結構描述漂移和取樣的選項](media/data-flow/source1.png "公用來源 1")

### <a name="sample-the-data"></a>取樣資料
啟用**取樣**來限制從來源的資料列數目。 當您測試，或從您的來源，以進行偵錯範例資料，請使用此設定。

## <a name="define-schema"></a>定義結構描述

當您的來源檔案不強型別 （適用於範例中，一般檔案而非 Parquet 檔案），在 來源轉換中定義以下每個欄位的資料類型。  

![來源轉換定義結構描述 索引標籤上的設定](media/data-flow/source2.png "來源 2")

您之後可以變更選取的轉換中的資料行名稱。 若要變更的資料類型使用的衍生資料行 」 轉換。 針對強型別來源，您可以修改稍後選取轉換中的資料類型。 

![在 選取轉換的資料型別](media/data-flow/source003.png "資料類型")

### <a name="optimize-the-source-transformation"></a>最佳化來源轉換

在 [**最佳化**索引標籤的來源轉換] 中，您可能會看到**來源**資料分割類型。 只有在您的來源是 Azure SQL Database 時，才使用此選項。 這是因為 Data Factory 會嘗試建立連接平行針對 SQL Database 來源執行大型查詢。

![來源資料分割設定](media/data-flow/sourcepart3.png "資料分割")

您不需要分割資料在 SQL Database 來源，但資料分割是針對大型查詢很有用。 您可以為您的資料分割基礎的資料行或查詢。

### <a name="use-a-column-to-partition-data"></a>使用資料行來分割資料

從來源資料表中，選取 資料分割的資料行上。 也請設定資料分割數目。

### <a name="use-a-query-to-partition-data"></a>使用查詢來分割資料

您可以選擇資料分割以查詢為基礎的連線。 只要輸入 WHERE 述詞的內容。 例如，請輸入 > 1980 年。

## <a name="source-file-management"></a>來源檔案管理

選擇要管理您的來源中的檔案設定。 

![新的來源設定](media/data-flow/source2.png "新設定")

* **萬用字元路徑**:從您的來源資料夾，選擇一系列符合模式的檔案。 此設定會覆寫您的資料集定義中的任何檔案。

萬用字元範例：

* ```*``` 代表任何字元的組合
* ```**``` 表示遞迴目錄巢狀結構
* ```?``` 將一個字元取代
* ```[]``` 比對一個括號中的多個字元

* ```/data/sales/**/*.csv``` 取得下 /data/sales 的所有 csv 檔案
* ```/data/sales/20??/**``` 取得在 20 世紀中的所有檔案
* ```/data/sales/2004/*/12/[XY]1?.csv``` 取得在 2004 年的所有 csv 檔案，在 12 月起 X 或 Y 2 位數的前置詞

容器的指定資料集內。 萬用字元路徑因此也必須包含您在根資料夾中的資料夾路徑。

* **檔案清單**:這是檔案集合。 建立文字檔案，其中包含要處理的相對路徑檔案的清單。 指向此文字檔案。
* **若要儲存檔案名稱的資料行**:在您的資料中的資料行中儲存的原始程式檔的名稱。 在此輸入新的名稱以儲存檔案名稱字串。
* **完成後**:選擇資料流程回合，刪除原始程式檔，或移動的原始程式檔之後的原始程式檔不執行任何動作。 移動路徑是相對的。

若要移動到另一個位置的後置處理的原始程式檔，先選取 「 移動 」 檔案作業。 然後，設定 [從] 目錄。 如果您不會使用任何萬用字元，您的路徑，然後在 [從] 設定將會與您的來源資料夾相同的資料夾。

如果您有一個含萬用字元來源路徑，例如：

```/data/sales/20??/**/*.csv```

您可以指定為 「 來源 」

```/data/sales```

和"to"做為

```/backup/priorSales```

在此情況下，這來自 /data/sales 下的所有子目錄會都移動相對於 /backup/priorSales。

### <a name="sql-datasets"></a>SQL 資料集

如果您的來源是 SQL Database 或 SQL 資料倉儲中，您會有其他的來源檔案管理選項。

* **查詢**：對您的來源輸入 SQL 查詢。 此設定會覆寫任何您選擇在資料集中的資料表。 請注意， **Order By**子句不支援在這裡，但是您可以設定的完整 SELECT FROM 陳述式。 您也可以使用使用者定義資料表函式。 **選取 * 從 udfGetData()** 是傳回資料表的 SQL 中的 UDF。 此查詢會產生您可以在資料流程中使用的來源資料表。
* **批次大小**：輸入將大型資料讀取到批次大小。

> [!NOTE]
> 只有在您開始從管線執行 （管線偵錯或執行的執行），以使用執行資料流活動的管線中的資料流程時，才，就會執行檔案作業。 檔案作業*沒有*資料流程偵錯模式執行。

### <a name="projection"></a>投射

中的資料集的結構描述，例如在來源中的投影定義資料行、 類型和從來源資料的格式。 

![在 [投影] 索引標籤上的設定](media/data-flow/source3.png "投影")

如果您的文字檔案沒有定義的結構描述，請選取**偵測資料類型**以便將範例資料處理站，並將其推論資料型別。 選取 **定義的預設格式**若要自動偵測的預設資料格式。 

您可以修改稍後衍生資料行 」 轉換中的資料行資料類型。 使用選取的轉換，修改資料行名稱。

![設定預設資料格式](media/data-flow/source2.png "預設格式")

## <a name="next-steps"></a>後續步驟

馬上開始建置[derived column 轉換](data-flow-derived-column.md)並[選取 轉換](data-flow-select.md)。
