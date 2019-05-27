---
title: Azure Data Factory 中的對應資料流程的效能和微調指南 |Microsoft Docs
description: 深入了解 Azure Data Factory 中的資料流程效能的影響，當您使用對應的資料流時的關鍵因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172336"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>對應資料的流程效能及微調指南

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory 對應資料流程提供設計、 部署和協調大規模的資料轉換的無程式碼的瀏覽器介面。

> [!NOTE]
> 如果您不熟悉如何使用 ADF 對應資料流動一般情況下，請參閱[資料的流程概觀](concepts-data-flow-overview.md)之前先閱讀這篇文章。
>

> [!NOTE]
> 當您設計和測試資料流，從 ADF UI 時，請務必開啟偵錯參數讓您可以執行您的資料流中即時而不需等待叢集準備項目。
>

![偵錯 按鈕](media/data-flow/debugb1.png "偵錯")

## <a name="optimizing-for-azure-sql-database"></a>Azure SQL Database 的最佳化

![來源組件](media/data-flow/sourcepart2.png "來源組件")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>您可以對應到您來源資料庫資料分割的資料分割以 「 來源 」 轉換中的資料庫資料表資料行索引鍵為基礎的 Spark

* 請移至 「 最佳化 」，然後選取 「 來源 」。 在查詢中設定特定的資料表資料行或類型。
* 如果您選擇 [資料行]，然後挑選資料分割資料行。
* 此外，設您的 Azure SQL DB 的連線數目上限。 您可以嘗試較高的設定，以取得平行連線到您的資料庫。 不過，某些情況下可能會導致更快的效能，數量有限的連線。

### <a name="set-batch-size-and-query-on-source"></a>設定批次大小與來源上的查詢

![來源](media/data-flow/source4.png "來源")

* 設定批次大小，會指示 ADF 將資料儲存在記憶體中而不是由資料列的資料列集。 它是選擇性的設定，您可能執行資源不足的計算節點上若不正確大小。
* 設定查詢，可以讓您篩選來源端的資料列右側，即使到達適用於資料流程進行處理，可以進行更快速的初始資料擷取之前。
* 如果您使用查詢時，您就可以為您的 Azure SQL DB，也就是 READ UNCOMMITTED 新增選擇性的查詢提示

### <a name="set-sink-batch-size"></a>設定接收批次大小

![接收](media/data-flow/sink4.png "接收")

* 為了避免資料 floes 逐列處理，請在 接收設定，適用於 Azure SQL DB 中設定 「 批次大小 」。 這會告知 ADF 來處理資料庫寫入提供的大小為基礎的批次。

### <a name="set-partitioning-options-on-your-sink"></a>設定資料分割的接收器上的選項

* 即使您沒有在目的地 Azure SQL DB 資料表分割資料，請移至 [最佳化] 索引標籤，並設定資料分割。
* 通常，只告訴 ADF 使用執行中的 Spark 叢集上的分割區更快的資料載入不會強迫單一節點/資料分割中的所有連線會導致循環配置資源。

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>增加您 Azure 整合執行階段中的計算引擎的大小

![新的 IR](media/data-flow/ir-new.png "新 IR")

* 增加將增加節點數目，並提供您更多的處理能力來查詢及寫入您的 Azure SQL DB 的核心數目。
* 請嘗試將套用到計算節點的更多資源的 「 計算最佳化 」 和 「 記憶體最佳化 」 選項。

### <a name="disable-indexes-on-write"></a>寫入停用索引
* 使用 ADF 管線預存程序活動之前您資料流程的活動，以停用您會接收從正在寫入的目標資料表上的索引。
* 之後您資料流程的活動，將啟用這些索引的另一個預存程序活動。

### <a name="increase-the-size-of-your-azure-sql-db"></a>增加您的 Azure SQL DB 大小
* 排程調整大小，您的來源和接收 Azure SQL DB，您的管線，以提高輸送量及降低 Azure 節流，一旦達到 DTU 限制您執行之前。
* 您的管線執行完成之後，您可以調整您的資料庫回到其正常的執行率。

## <a name="next-steps"></a>後續步驟
請參閱其他資料流程的文章：

- [資料的流程概觀](concepts-data-flow-overview.md)
- [Data Flow 活動](control-flow-execute-data-flow-activity.md)

