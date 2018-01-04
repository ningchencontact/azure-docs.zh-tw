---
title: "如何在 Azure Data Factory 中建立觸發程序 |Microsoft 文件"
description: "了解如何建立根據排程執行管線的 Azure Data Factory 中的觸發程序。"
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
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>如何建立根據排程執行管線的觸發程序
本文章提供建立、 啟動及監視觸發程序的步驟。 如需觸發程序的概念資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md)。

> [!NOTE]
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的 Data Factory 服務 (也就是正式推出版 (GA))，請參閱 [開始使用 Data Factory 第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
本節說明如何使用 Azure PowerShell 來建立、 啟動及監視觸發程序。 如果您想要查看此範例的工作，會先經過[快速入門： 建立使用 Azure PowerShell 的 data factory](quickstart-create-data-factory-powershell.md)。 然後，將下列程式碼加入至 main 方法，後者則會建立並啟動每隔 15 分鐘執行一次的排程觸發程序。 觸發程序都與管線 (**Adfv2QuickStartPipeline**) 所建立的快速入門的步驟一部分。

1. 建立名為 MyTrigger.json C:\ADFv2QuickStartPSH\ 資料夾具有下列內容中的 JSON 檔案： 

    > [!IMPORTANT]
    > 設定**startTime**到目前的 UTC 時間和**endTime**為一小時超過目前的 UTC 時間儲存 JSON 檔案之前。

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```
    
    在 JSON 片段： 
    - **類型**觸發程序設定為**ScheduleTrigger**。 
    - **頻率**設**分鐘**和**間隔**設**15**。 因此，觸發程序執行管線的開始和結束時間之間每隔 15 分鐘。 
    - **EndTime**是一小時之後**startTime**，因此觸發程序執行管線 15 分鐘、 30 分鐘，並後 startTime 45 分鐘。 請務必更新至目前的 UTC 時間和結束時間為過去的開始時間的一小時的開始時間。  
    - 觸發程序相關聯**Adfv2QuickStartPipeline**管線。 若要關聯多個管線與觸發程序，新增更多**pipelineReference**區段。 
    - 快速入門中的管線會採用兩個**參數**。 因此，您會從觸發程序傳遞這些參數的值。 
2. 建立觸發程序使用**組 AzureRmDataFactoryV2Trigger** cmdlet。

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. 確認觸發程序狀態為**已停止**使用**Get AzureRmDataFactoryV2Trigger** cmdlet。 

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

## <a name="use-net-sdk"></a>使用 .NET SDK
本節說明如何使用.NET SDK 來建立、 啟動及監視觸發程序。 如果您想要查看使用這個程式碼時，會先經過[建立 data factory，使用.NET SDK 快速入門](quickstart-create-data-factory-dot-net.md)。 然後，將下列程式碼加入至 main 方法，後者則會建立並啟動每隔 15 分鐘執行一次的排程觸發程序。 觸發程序都與管線 (**Adfv2QuickStartPipeline**) 所建立的快速入門的步驟一部分。 

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method. 
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

若要監視執行的觸發程序，加入下列程式碼的最後一個以外`Console.WriteLine`陳述式： 

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

若要監視在 Azure 入口網站中的觸發程序執行/管線執行，請參閱[監視器管線會執行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>使用 Python SDK
本節說明如何使用 Python SDK 來建立、 啟動及監視觸發程序。 如果您想要查看使用這個程式碼時，會先經過[建立 data factory 使用 Python SDK 快速入門](quickstart-create-data-factory-python.md)。 然後，加入下列程式碼區塊之後 python 指令碼的 監視管線執行 「 程式碼區塊。 此程式碼會建立排程觸發程序執行每隔 15 分鐘之間指定的開始和結束時間。 更新 start_time 為目前的 UTC 時間，然後為目前的 UTC 時間超過一小時 end_time。 

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC') 
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
若要監視在 Azure 入口網站中的觸發程序執行/管線執行，請參閱[監視器管線會執行](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>使用 Resource Manager 範本
您可以使用 Azure Resource Manager 範本來建立觸發程序。 如需逐步指示，請參閱[建立 Azure data factory 使用資源管理員範本](quickstart-create-data-factory-resource-manager-template.md)。  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>傳遞到管線的觸發程序開始時間
在第 1 版中，Azure Data Factory 支援使用 SliceStart/SliceEnd/WindowStart/WindowEnd 系統變數讀取或寫入分割的資料。 在第 2 版中，您可以透過使用管線參數，以觸發程序的開始時間/已排程的時間作為參數的值來達成此行為。 在下列範例中，觸發程序的排程的時間做為值傳遞給管線參數 scheduledRunTime。 

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
如需詳細資訊，請參閱[如何讀取或寫入資料分割資料](how-to-read-write-partitioned-data.md)。

## <a name="next-steps"></a>後續步驟
如需觸發程序的詳細資訊，請參閱[管線執行和觸發程序](concepts-pipeline-execution-triggers.md#triggers)。