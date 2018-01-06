---
title: "如何在 Azure Data Factory 中建立輪轉視窗觸發程序 |Microsoft 文件"
description: "了解如何在 Azure Data Factory 管線執行輪轉視窗上建立觸發程序。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>如何建立執行管線輪轉視窗的觸發程序
本文章提供建立、 啟動及監視的輪轉視窗觸發程序的步驟。 如需觸發程序以及我們所支援的類型的一般資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md)。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [開始使用 Data Factory 第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

輪轉視窗觸發程序是從指定的開始時間，同時還能保留狀態以週期性時間間隔就會引發的觸發程序的類型。 輪轉視窗是一系列的大小固定、 非重疊的連續時間間隔。 輪轉視窗觸發程序有 1:1 關聯性具有管線，並只能參考單一管線。

## <a name="tumbling-window-trigger-type-properties"></a>輪轉視窗觸發程序類型屬性
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

下表提供與循環以及排程輪轉視窗觸發程序中的主要項目的高階概觀。

| **JSON 名稱** | **說明** | **允許的值** | **必要** |
|:--- |:--- |:--- |:--- |
| **type** | 觸發程序的類型。 這被固定為"TumblingWindowTrigger。 」 | 字串 | 是 |
| **runtimeState** | <readOnly>可能的值： 啟動、 停止、 已停用 | 字串 | [是]，唯讀 |
| **frequency** |*頻率*字串，表示觸發程序還一再發生的頻率單位。 支援的值為 「 分 」 和 「 小時 」。 如果開始時間比頻率較精細的日期部分，它們將會列入考量，來計算視窗界限。 如 ex： 如果頻率為每小時和開始時間是 2016年-04-01T10:10:10Z，第一個視窗是 (2017年-09-01T10:10:10Z，2017年-09-01T11:10:10Z。)  | 字串。 支援的型別 「 分 」，「 小時 」 | 是 |
| **interval** |*間隔*這是一個正整數，並代表的間隔*頻率*，決定將會執行觸發程序的頻率。 例如，如果*間隔*是 3 和*頻率*是 「 小時 」，觸發程序會重複每 3 個小時。 | 整數  | 是 |
| **startTime**|*startTime* 是日期時間。 *startTime*是第一次出現且可以在過去。 第一個觸發程序間隔會 （startTime、 startTime + 間隔）。 | Datetime | 是 |
| **endTime**|*endTime*是日期時間。 *endTime*是最後一個出現項目，而且可以是在過去。 | Datetime | 是 |
| **延遲** | 指定資料處理的視窗啟動之前的延遲。 在預期的執行時間 + 延遲時間後啟動執行管線。 延遲會定義如何觸發程序在等待過期的時間之前觸發新的執行時間。 它不會改變時段開始時間。 | Timespan (範例： 00:10:00，表示延遲 10 分鐘) |  編號 預設值是"00: 00:00" |
| **最大並行** | 同時觸發程序執行準備的 windows 所引發的數目。 範例： 如果我們嘗試回填每小時的昨天，所應 24 windows。 如果並行 = 10，僅為前 10 的 windows 不會引發事件的觸發程序 (00:00-01:00-00:09:00-10)。 前 10 個的引動的管線執行完成之後，會針對接下來的 10 (10:00-11:00-19:00 20:00) 而引發觸發程序執行。 繼續進行的並行範例 = 10，10 windows 好時，會有 10 個管線執行時。 如果只有 1 視窗準備，只會有 1 管線執行。 | 整數  | 可以。 可能的值 1-50 |
| **retryPolicy： 計數** | 管線執行之前的重試次數被標示為 「 失敗 」  | 整數  |  編號 預設值為 0 的重試 |
| **retryPolicy: intervalInSeconds** | 重試嘗試之間的延遲 (秒) | 整數  |  編號 預設為 30 秒 |

### <a name="using-system-variables-windowstart-and-windowend"></a>使用系統變數： WindowStart 和 WindowEnd

如果您想要使用 WindowStart 和 WindowEnd 輪轉視窗觸發程序，在您**管線**定義 （也就是適用於查詢的一部分），您必須將變數當做參數傳遞至管線中**觸發程序**定義，就像這樣：
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

然後，在管線定義中，若要使用的 WindowStart 和 WindowEnd 值，使用您的參數據以"MyWindowStart"和"MyWindowEnd"

### <a name="notes-on-backfill"></a>附註的回填
執行 （尤其是在回填案例） 進行多個視窗時，執行 windows 的順序具決定性，而且會從最舊到最新的間隔。 沒有任何方法來變更此行為從現在開始。

### <a name="updating-an-existing-triggerresource"></a>更新現有的 TriggerResource
* 視窗的狀態變更觸發程序的頻率 （或視窗大小），如果已經處理將*不*重設。 觸發程序會繼續執行它使用新的視窗大小的最後一個 windows 引發。
* 如果觸發程序變更 （新增或更新），已經視窗的狀態結束時間處理將*不*重設。 觸發程序只會接受新的結束時間。 如果結束時間之前已執行的 windows 中，將會停止觸發程序。 否則，就會停止時遇到新的結束時間。

## <a name="sample-using-azure-powershell"></a>使用 Azure PowerShell 範例
本節說明如何使用 Azure PowerShell 來建立、 啟動及監視觸發程序。

1. 建立名為 MyTrigger.json C:\ADFv2QuickStartPSH\ 資料夾具有下列內容中的 JSON 檔案：

   > [!IMPORTANT]
   > 設定**startTime**到目前的 UTC 時間和**endTime**為一小時超過目前的 UTC 時間儲存 JSON 檔案之前。

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  
2. 建立觸發程序使用**組 AzureRmDataFactoryV2Trigger** cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. 使用啟動觸發程序**開始 AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. 確認觸發程序狀態為**已啟動**使用**Get AzureRmDataFactoryV2Trigger** cmdlet。

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  取得觸發程序執行，使用 PowerShell 來**Get AzureRmDataFactoryV2TriggerRun** cmdlet。 若要取得觸發程序執行的相關資訊，請執行下列命令會定期： 更新**TriggerRunStartedAfter**和**TriggerRunStartedBefore**值，以符合觸發程序定義中的值.

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
若要監視在 Azure 入口網站中的觸發程序執行/管線執行，請參閱[監視器管線會執行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="next-steps"></a>後續步驟
如需觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#triggers)。
