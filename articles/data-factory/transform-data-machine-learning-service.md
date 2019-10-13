---
title: 在您的 Azure Data Factory 管線中執行 Azure Machine Learning 服務管線 |Microsoft Docs
description: 瞭解如何在您的 Azure Data Factory 管線中執行 Azure Machine Learning 服務管線。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: ddaa020b3567f8f00cfd7fc7f7728f1160d9306b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301343"
---
# <a name="execute-azure-machine-learning-service-pipelines-in-azure-data-factory"></a>在 Azure Data Factory 中執行 Azure Machine Learning 服務管線

在您的 Azure Data Factory 管線中，以步驟的方式執行 Azure Machine Learning 服務管線。 Machine Learning 執行管線活動可啟用批次預測案例，例如識別可能的貸款預設值、判斷情感，以及分析客戶行為模式。

## <a name="syntax"></a>語法

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要項
-------- | ----------- | -------------- | --------
name | 管線中的活動名稱 | 字串 | 是
型別 | 活動類型為「AzureMLExecutePipeline」 | 字串 | 是
linkedServiceName | Azure Machine Learning 服務的連結服務 | 連結服務參考 | 是
mlPipelineId | 已發佈 Azure Machine Learning 管線的識別碼 | 字串 (或含有字串之 resultType 的運算式) | 是
experimentName | 執行 Machine Learning 管線執行的歷程記錄實驗名稱 | 字串 (或含有字串之 resultType 的運算式) | 否
mlPipelineParameters | 要傳遞至已發行 Azure Machine Learning 管線端點的索引鍵/值組。 索引鍵必須符合已發佈 Machine Learning 管線中定義之管線參數的名稱 | 具有索引鍵值組（或具有 resultType 物件的運算式）的物件 | 否
mlParentRunId | 父 Azure Machine Learning 服務管線執行識別碼 | 字串 (或含有字串之 resultType 的運算式) | 否
continueOnStepFailure | 當步驟失敗時，是否要繼續執行 Machine Learning 管線中的其他步驟 | boolean | 否

## <a name="next-steps"></a>後續步驟
請參閱下列文章，其說明如何以其他方式轉換資料：

* [執行資料流程活動](control-flow-execute-data-flow-activity.md)
* [U-SQL 活動](transform-data-using-data-lake-analytics.md)
* [Hive 活動](transform-data-using-hadoop-hive.md)
* [Pig 活動](transform-data-using-hadoop-pig.md)
* [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 串流活動](transform-data-using-hadoop-streaming.md)
* [Spark 活動](transform-data-using-spark.md)
* [.NET 自訂活動](transform-data-using-dotnet-custom-activity.md)
* [預存程序活動](transform-data-using-stored-procedure.md)
