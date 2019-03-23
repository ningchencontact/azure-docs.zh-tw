---
title: Azure Data Factory 對應資料流程資料集
description: Azure Data Factory 對應資料流程 」 有特定的資料集的相容性
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 4e36e96947e6a8595230023065eb9f44a5a1f3d2
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371310"
---
# <a name="mapping-data-flow-datasets"></a>對應資料流程資料集

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

資料集是一種 Data Factory 建構，可定義您在管線中使用的資料形狀。 在「資料流程」中，資料列和資料行層級資料會需要細微的資料集定義。 在控制流程管線中使用的資料集不需要相同程度的資料了解。

來源和接收器的轉換中使用資料流程中的資料集。 它們用來定義的基本資料結構描述。 如果您的資料中沒有結構描述，您可以將來源與接收的「結構描述漂移」設為開啟。 透過從資料集定義的結構描述，您將會具有來自相關聯「已連結的服務」的相關資料類型、資料格式、檔案位置和連接資訊。 做為來源 「 投射 」，從資料集的中繼資料會出現在 來源轉換。 中的資料集的結構描述代表的實體資料類型和形狀，而投影來源轉換中的表示資料流程表示法，以定義的名稱和類型的資料。

## <a name="dataset-types"></a>資料集類型

目前在資料流程中，您會看到四個資料集類型：

* Azure SQL DB
* Azure SQL DW
* Parquet （從 ADLS 和 Blob）
* （從 ADLS 和 Blob） 分隔的文字

個別的資料流資料集*來源類型*從*連結的服務連線類型*。 通常在 Data Factory，您可以選擇連接類型 (Blob、ADLS 等)，然後在資料集中定義檔案類型。 在「資料流程」內部，您將挑選來源類型，這會與不同的「已連結的服務」連接類型相關聯。

![來源轉換選項](media/data-flow/dataset1.png "來源")

## <a name="data-flow-compatible-datasets"></a>資料流程相容的資料集

當建立新資料集時，面板右上角會有一個名為「資料流程相容」的核取方塊。 按一下該按鈕將僅篩選出可與「資料流程」搭配使用的資料集。 

## <a name="import-schemas"></a>匯入結構描述

當匯入「資料流程」資料集的結構描述時，您會看到的 [匯入結構描述] 按鈕。 按一下按鈕將顯示兩個選項：從來源匯入或從本機檔案匯入。 在大部分情況下，您會直接從來源匯入結構描述。 不過，如果您有現有的結構描述檔案 (含標頭的 Parquet 檔案或 CSV)，您可以指向該本機檔案，Data Factory 將會根據該結構描述檔案定義結構描述。

## <a name="create-new-table"></a>建立新資料表

在資料流程中，您可以提出 ADF 到目標資料庫中建立新的資料表定義，藉由設定中有新的資料表名稱的 「 接收 」 轉換的資料集。 在 SQL 資料集，按一下 [編輯] 下方的資料表名稱並輸入新的資料表名稱。 然後，在 「 接收 」 轉換中，開啟 「 允許結構描述漂移 」。 Seth 「 匯入結構描述 」 設定為 None。

![來源轉換結構描述](media/data-flow/dataset2.png "SQL 結構描述")

## <a name="delimited-text-dataset"></a>分隔的文字資料集

分隔的文字資料集，在中，您將設定的分隔符號來處理任一單一分隔符號 ('\t' TSV'，' 的 CSV，' |'...)，或使用多個字元的分隔符號。 將標頭資料列切換設定，然後進入 「 來源 」 轉換，來自動偵測資料類型。

## <a name="next-steps"></a>後續步驟

著手[建立新的 Data Flow](data-flow-create.md)和加入來源轉換。 然後設定您的來源資料集。

使用[複製活動](copy-activity-overview.md)才能匯入資料，從任何 ADF 資料來源，並存取資料流程的該屬性暫存 ADLS 或 Blob 中。

