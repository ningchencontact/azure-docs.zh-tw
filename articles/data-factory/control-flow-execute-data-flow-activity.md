---
title: Azure Data Factory 中的資料流程活動 |Microsoft Docs
description: 如何從 data factory 管線內部執行資料流程。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: makromer
ms.date: 10/07/2019
ms.openlocfilehash: cbfa1acac34187263f8c4203e41bbe61d7e4c745
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030520"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure Data Factory 中的資料流程活動

使用資料流程活動，透過對應資料流程來轉換和移動資料。 如果您不熟悉資料流程，請參閱[對應資料流程總覽](concepts-data-flow-overview.md)

## <a name="syntax"></a>語法

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要項
-------- | ----------- | -------------- | --------
資料流程 | 正在執行之資料流程的參考 | DataFlowReference | 是
integrationRuntime | 資料流程執行所在的計算環境 | IntegrationRuntimeReference | 是
暫存。 linkedService | 如果您使用的是 SQL DW 來源或接收，用於 PolyBase 暫存的儲存體帳戶 | LinkedServiceReference | 只有在資料流程讀取或寫入至 SQL DW 時
暫存。 folderPath | 如果您使用的是 SQL DW 來源或接收，則為用於 PolyBase 暫存的 blob 儲存體帳戶中的資料夾路徑 | 字串 | 只有在資料流程讀取或寫入至 SQL DW 時

![執行資料流程](media/data-flow/activity-data-flow.png "執行")資料流程

### <a name="data-flow-integration-runtime"></a>資料流程整合執行時間

選擇要用於資料流程活動執行的 Integration Runtime。 根據預設，Data Factory 將會使用具有四個背景工作角色核心且沒有存留時間（TTL）的「自動解析」 Azure Integration runtime。 此 IR 具有一般目的計算類型，並會在與您的處理站相同的區域中執行。 您可以建立自己的 Azure 整合執行時間，以定義您的資料流程活動執行的特定區域、計算類型、核心計數和 TTL。

就管線執行而言，叢集是一種作業叢集，需要幾分鐘的時間才能開始執行。 如果未指定 TTL，每次執行管線時都需要此啟動時間。 如果您指定 TTL，暖叢集集區會在最後一次執行後指定的時間保持作用中狀態，因而縮短啟動時間。 例如，如果您有60分鐘的 TTL，並每隔一小時執行一次資料流程，叢集集區就會保持作用中狀態。 如需詳細資訊，請參閱[Azure 整合運行](concepts-integration-runtime.md)時間。

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> [資料流程] 活動中的 Integration Runtime 選取專案僅適用于已*觸發*的管線執行。 在偵錯工具中指定的叢集上執行資料流程的管線處理。

### <a name="polybase"></a>PolyBase

如果您使用 Azure SQL 資料倉儲做為接收或來源，則必須為您的 PolyBase 批次負載選擇預備位置。 PolyBase 允許大量批次載入，而不是逐列載入資料。 PolyBase 可大幅減少載入 SQL DW 的時間。

## <a name="parameterizing-data-flows"></a>參數化資料流程

### <a name="parameterized-datasets"></a>參數化資料集

如果您的資料流程使用參數化資料集，請在 [**設定**] 索引標籤中設定參數值。

![執行資料流程參數](media/data-flow/params.png "參數")

### <a name="parameterized-data-flows"></a>參數化資料流程

如果您的資料流程已參數化，請在 [**參數**] 索引標籤中設定資料流程參數的動態值。您可以使用 ADF 管線運算式語言（僅適用于字串類型）或資料流程運算式語言來指派動態或常值參數值。 如需詳細資訊，請參閱[資料流程參數](parameters-data-flow.md)。

![執行資料流程參數範例](media/data-flow/parameter-example.png "參數範例")

## <a name="pipeline-debug-of-data-flow-activity"></a>資料流程活動的管線 debug

若要使用「資料流程」活動執行「調試管線」，您必須在頂端列上透過 [資料流程 **] [資料流程**處理] 滑杆切換至 [資料流程] [調試] 模式。 [偵錯工具] 模式可讓您針對作用中的 Spark 叢集執行資料流程。 如需詳細資訊，請參閱[Debug Mode](concepts-data-flow-debug-mode.md)。

![調試 按鈕](media/data-flow/debugbutton.png "調試 按鈕")

Debug 管線會針對使用中的 debug 叢集執行，而不是在 [資料流程] 活動設定中指定的整合執行時間環境。 您可以在啟動 [debug] 模式時選擇 [debug compute] 環境。

## <a name="monitoring-the-data-flow-activity"></a>監視資料流程活動

資料流程活動具有特殊的監視體驗，您可以在其中查看資料分割、階段時間和資料歷程資訊。 透過 [**動作**] 底下的 [眼鏡] 圖示開啟 [監視中] 窗格。 如需詳細資訊，請參閱[監視資料流程](concepts-data-flow-monitoring.md)。

## <a name="next-steps"></a>後續步驟

請參閱 Data Factory 支援的控制流程活動： 

- [If Condition 活動](control-flow-if-condition-activity.md)
- [執行管道活動](control-flow-execute-pipeline-activity.md)
- [For Each 活動](control-flow-for-each-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [Web 活動](control-flow-web-activity.md)
- [Until 活動](control-flow-until-activity.md)
