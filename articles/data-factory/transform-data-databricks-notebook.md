---
title: 使用 Azure Databricks Notebook 來轉換資料 | Microsoft Docs
description: 了解如何藉由執行 Databricks Notebook 來處理或轉換資料。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: fbf713b2d52469ae12fc284e0a3d7e3bc369daeb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620498"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>執行 Databricks Notebook 來轉換資料

[Data Factory 管線](concepts-pipelines-activities.md)中的 Azure Databricks Notebook 活動會在 Azure Databricks 工作區中執行 Databricks Notebook。 本文是根據 [資料轉換活動](transform-data.md) 一文，它呈現資料轉換和支援的轉換活動的一般概觀。 Azure Databricks 是用於執行 Apache Spark 的受控平台。

## <a name="databricks-notebook-activity-definition"></a>Databricks Notebook 活動定義

以下是 Databricks Notebook 活動的 JSON 定義範例：

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks Notebook 活動屬性

下表說明 JSON 定義中使用的 JSON 屬性：

|屬性|說明|必要|
|---|---|---|
|name|管線中的活動名稱。|yes|
|說明|說明活動用途的文字。|否|
|type|若是 Databricks Notebook 活動，則活動類型是 DatabricksNotebook。|yes|
|預設容器|Databricks Notebook 執行所在之 Databricks 連結服務的名稱。 若要深入了解此已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。|yes|
|notebookPath|要在 Databricks 工作區中執行之 Notebook 的絕對路徑。 此路徑必須以斜線開頭。|yes|
|baseParameters|機碼值組的陣列。 基礎映像參數可以用於每個活動執行。 如果 Notebook 採用未指定的參數，則系統會使用 Notebook 的預設值。 在 [Databricks Notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair) 中尋找更多參數的相關資料。|否|
