---
title: Azure Data Factory 對應資料流程接收轉換
description: Azure Data Factory 對應資料流程接收轉換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 381dc2f9f6d3a074af00ba047472719c086f5811
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408403"
---
# <a name="mapping-data-flow-sink-transformation"></a>對應資料流程接收轉換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![接收選項](media/data-flow/windows1.png "接收 1")

在資料流程轉換完成時，您可以將已轉換的資料接收到目的地資料集中。 在「接收」轉換中，您可以選擇您要用於目的地輸出資料的資料集定義。 您可以有資料流程所需數量的接收轉換。

可供變更傳入資料及結構描述漂移的常見做法，就是將輸出資料接收到資料夾，而不需要在輸出資料集中定義的結構描述。 您可藉由在來源選取 [允許結構描述漂移]，另外處理您來源中的所有資料行變更，然後自動對應的「接收」中的所有欄位。

您可以在接收到資料集時，選擇覆寫、附加資料流程或使資料流程失效。

您也可以選擇「自動對應」以接收所有傳入的欄位。 如果您希望選擇您想接收至目的地的欄位，或想要變更目的地的欄位名稱，請針對 [自動對應] 選擇 [關閉]，然後按一下 [對應] 索引標籤來對應輸出欄位：

![接收選項](media/data-flow/sink2.png "接收 2")

## <a name="output-to-one-file"></a>輸出至一個檔案
對於 Azure 儲存體 Blob 或 Data Lake 接收類型，您將會已轉換的資料輸出到資料夾中。 Spark 會根據「接收」轉換中使用的資料分割配置，產生已分割的輸出資料檔案。 您可以按一下 [最佳化] 索引標籤，以設定資料分割配置。如果您希望 ADF 將您的輸出合併成單一檔案，請按一下 [單一資料分割] 選項按鈕。

![接收選項](media/data-flow/opt001.png "接收選項")

### <a name="output-settings"></a>輸出設定

覆寫將會截斷資料表 (若有的話)，然後加以重建並載入資料。 附加會插入新的資料列。 如果「資料集」資料表名稱中的資料表不存在於目標 ADW 中，則資料流程會建立資料表，然後載入資料。

如果您取消選取 [自動對應]，即可手動將欄位對應至目的地資料表。

![接收 ADW 選項](media/data-flow/adw2.png "adw 接收")

#### <a name="field-mapping"></a>欄位對應

在接收轉換的 [對應] 索引標籤上，您可以將傳入 (左側) 資料行對應到目的地 (右側)。 當您將資料流程接收到檔案時，ADF 一律會將新檔案寫入資料夾。 當您對應到資料庫資料集時，您可以選擇利用此結構描述產生新的資料表 (將「儲存原則」設定為 [覆寫])，或將新資料列插入現有資料表中，並將欄位對應到現有的結構描述。

您可以在對應資料表中使用多選功能，按一下即可連結多個資料行、取消連結多個資料行，或將多個資料列對應到相同的資料行名稱。

![欄位對應](media/data-flow/multi1.png "多個選項")

如果您想要重設您的資料行對應，請按 [重新對應] 按鈕來重設對應。

![連線](media/data-flow/maxcon.png "連線")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>ADF V2 GA 版本的接收轉換更新

![接收選項](media/data-flow/sink1.png "接收一")

![接收選項](media/data-flow/sink2.png "接收")

* 讓 [結構描述漂移] 和 [驗證結構描述] 選項現已在「接收」中提供。 這可讓您指示 ADF 完全接受彈性結構描述定義 (結構描述漂移)，而如果結構描述變更 (驗證結構描述)，則讓「接收」失效。

* 清除資料夾。 ADF 會在目標資料夾中寫入目的地檔案之前，先截斷接收資料夾內容。

* 檔案名稱選項

   * 預設值：可讓 Spark 根據 PART 預設值來命名檔案
   * 模式：輸入輸出檔案的名稱
   * 每個分割區：輸入每個分割區的檔案名稱
   * 作為資料行中的資料：將輸出檔案設定為資料行的值

> [!NOTE]
> 檔案作業只會在您執行「執行資料流程」活動時執行，而不是在「資料流程偵錯」模式中執行

使用 SQL 接收類型，您可以設定：

* 截斷資料表
* 重新建立資料表 (執行卸除/建立)
* 大型資料載入的批次大小。 輸入一個數字，以將寫入填入多個區塊。

![欄位對應](media/data-flow/sql001.png "SQL 選項")

## <a name="next-steps"></a>後續步驟

既然您已建立資料流程，請將[執行資料流程活動新增到您的管線](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview)。
