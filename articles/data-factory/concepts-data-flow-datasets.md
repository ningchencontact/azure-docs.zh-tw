---
title: Azure Data Factory 對應資料流程資料集
description: Azure Data Factory 對應資料流程具有特定的資料集相容性
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408743"
---
# <a name="mapping-data-flow-datasets"></a>對應資料流程資料集

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

資料集是一種 Data Factory 建構，可定義您在管線中使用的資料形狀。 在「資料流程」中，資料列和資料行層級資料會需要細微的資料集定義。 在控制流程管線中使用的資料集不需要相同程度的資料了解。

「資料流程來源與接收」轉換中的資料集用於定義基本資料結構描述。 如果您的資料中沒有結構描述，您可以將來源與接收的「結構描述漂移」設為開啟。 透過從資料集定義的結構描述，您將會具有來自相關聯「已連結的服務」的相關資料類型、資料格式、檔案位置和連接資訊。

## <a name="dataset-types"></a>資料集類型

目前在資料流程中，您會看到四個資料集類型：

* Azure SQL DB
* Azure SQL DW
* Parquet
* 分隔符號文字

資料流程資料集會將來源*類型*從「已連結的服務」連接類型中分開。 通常在 Data Factory，您可以選擇連接類型 (Blob、ADLS 等)，然後在資料集中定義檔案類型。 在「資料流程」內部，您將挑選來源類型，這會與不同的「已連結的服務」連接類型相關聯。

![來源轉換選項](media/data-flow/dataset1.png "來源")

## <a name="data-flow-compatible-datasets"></a>資料流程相容的資料集

當建立新資料集時，面板右上角會有一個名為「資料流程相容」的核取方塊。 按一下該按鈕將僅篩選出可與「資料流程」搭配使用的資料集。 

## <a name="import-schemas"></a>匯入結構描述

當匯入「資料流程」資料集的結構描述時，您會看到的 [匯入結構描述] 按鈕。 按一下按鈕將顯示兩個選項：從來源匯入或從本機檔案匯入。 在大部分情況下，您會直接從來源匯入結構描述。 不過，如果您有現有的結構描述檔案 (含標頭的 Parquet 檔案或 CSV)，您可以指向該本機檔案，Data Factory 將會根據該結構描述檔案定義結構描述。

