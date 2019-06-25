---
title: 執行 Azure Data Factory 中的資料流活動 |Microsoft Docs
description: 如何執行資料的流程從 data factory 管線中。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: e75c6290474d876ca22b5888d06b1fc0e4c8cd05
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077315"
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

### <a name="debugging-pipelines-with-data-flows"></a>偵錯資料流程管線

![偵錯 按鈕](media/data-flow/debugbutton.png "偵錯 按鈕")

利用適用於以互動方式執行管線偵錯版中測試您的資料流的 warmed 的叢集，使用資料流程偵錯。 您可以使用管線進行偵錯的選項來測試管線中的資料流程。

### <a name="run-on"></a>執行位置

這是必要的欄位定義要用於您的 資料流程的活動執行的整合執行階段。 根據預設，Data Factory 會使用預設的自動解決 Azure 整合執行階段。 不過，您可以建立您的 Azure 整合執行階段定義特定區域，您的資料流活動執行的計算類型、 核心計數和 TTL。

Data Flow 執行的預設值是 8 核心的一般計算，當 TTL 為 60 分鐘的時間。

選擇在資料流程的此執行的計算環境。 預設值是 Azure 自動解析預設整合執行階段。 這項選擇會在相同的區域，為您的 data factory 中的 Spark 環境上執行資料流程的資料。 計算類型會是作業的叢集，這表示計算環境將會需要幾分鐘時間來啟動。

您的資料流活動的 Spark 執行環境的控制權。 在  [Azure 整合執行階段](concepts-integration-runtime.md)是設定來設定的計算類型 （一般用途、 記憶體最佳化，並計算最佳化），背景工作角色核心數目和存留時間以符合與您的資料流計算的執行引擎需求。 此外，設定 TTL 可讓您維護暖立即可供工作執行的叢集。

![Azure 整合執行階段](media/data-flow/ir-new.png "Azure 整合執行階段")

> [!NOTE]
> 將資料流程的活動中的整合執行階段選取項目僅適用於*觸發執行*的管線。 使用偵錯，偵錯資料流程管線，將針對 8 核心預設的 Spark 叢集中執行。

### <a name="staging-area"></a>臨時區域

如果您在快要融化資料到 Azure 資料倉儲，您必須選擇預備環境位置，針對 Polybase 批次負載。 暫存設定僅適用於 Azure 資料倉儲工作負載。

## <a name="parameterized-datasets"></a>參數化資料集

如果您使用參數化資料集，請務必設定參數值。

![執行資料流參數](media/data-flow/params.png "參數")

### <a name="debugging-parameterized-data-flows"></a>偵錯的參數化的資料流

您可以只偵錯管線偵錯使用 execute 資料流程活動來執行參數化資料集的資料流。 目前，在 ADF 資料流程 」 中的互動式偵錯工作階段不適用於參數化資料集。 管線執行和偵錯回合會使用參數。

理想的作法是建立與靜態的資料集的資料流程，讓您在設計階段有可用的完整中繼資料資料行傳播。 然後當您實作您的資料流管線時，請使用動態參數化資料集取代靜態的資料集。

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [If Condition 活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
