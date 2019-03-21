---
title: 執行 Azure Data Factory 中的資料流活動 |Microsoft Docs
description: 執行資料流活動會執行資料流。
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792407"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Azure Data Factory 中執行資料的流程活動
用於執行資料流活動觸發的管線執行和管線偵錯 (sandbox) 執行中執行您的 ADF 資料流。

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>語法

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>類型屬性

* ```dataflow``` 是您想要執行的資料流量實體的名稱
* ```compute``` 描述的 Spark 執行環境
* ```coreCount``` 若要指派給此活動執行，在資料流程的核心數目

![執行資料流程](media/data-flow/activity-data-flow.png "執行資料流程")

### <a name="run-on"></a>執行位置

選擇在資料流程的此執行的計算環境。 預設值是 Azure 自動解析預設整合執行階段。 這項選擇會在相同的區域，為您的 data factory 中的 Spark 環境上執行資料流程的資料。 計算類型會是作業的叢集，這表示計算環境將會需要幾分鐘時間來啟動。

### <a name="debugging-pipelines-with-data-flows"></a>偵錯資料流程管線

![偵錯 按鈕](media/data-flow/debugbutton.png "偵錯 按鈕")

利用適用於以互動方式執行管線偵錯版中測試您的資料流的 warmed 的叢集，使用資料流程偵錯。 您可以使用管線偵錯選項來測試管線中的資料流程。

### <a name="compute-type"></a>計算類型

您可以選擇一般用途、 計算最佳化，或最佳化記憶體，在資料流程的需求而定。

### <a name="core-count"></a>核心計數

選擇您想要指派給作業的多少個核心。 對於較小的工作，較少的核心會配合更好。

### <a name="staging-area"></a>臨時區域

如果您在快要融化資料到 Azure 資料倉儲，您必須選擇預備環境位置，針對 Polybase 批次負載。

## <a name="parameterized-datasets"></a>參數化資料集

如果您使用參數化資料集，請務必設定參數值。

![執行資料流參數](media/data-flow/params.png "參數")

### <a name="debugging-parameterized-data-flows"></a>偵錯的參數化的資料流

您可以只偵錯管線偵錯使用 execute 資料流程活動來執行參數化資料集的資料流。 目前，在 ADF 資料流程 」 中的互動式偵錯工作階段不適用於參數化資料集。 管線執行和偵錯回合會使用參數。

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [If Condition 活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
