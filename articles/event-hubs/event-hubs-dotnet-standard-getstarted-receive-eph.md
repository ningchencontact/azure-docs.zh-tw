---
title: 使用 .NET Core 接收事件 - Azure 事件中樞 | Microsoft Docs
description: 本文會逐步解說如何建立 .NET Core 應用程式，以透過 EventProcessorHost 接收訊息。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 18b37fd7c85fced4cc57b57320daf195f6f33e3e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082375"
---
# <a name="get-started-receiving-messages-with-the-event-processor-host-in-net-core"></a>開始使用 .NET Core 中的事件處理器主機來接收訊息
「事件中樞」是一種服務，可處理來自連接裝置和應用程式的大量事件資料 (遙測)。 收集資料至「事件中樞」之後，可以使用存放裝置叢集來儲存資料，或使用即時分析提供者進行轉換。 此大規模事件收集和處理功能是新型應用程式架構 (包括物聯網 (IoT)) 的重要元件。 如需事件中樞的詳細概觀，請參閱[事件中樞概觀](event-hubs-about.md)和[事件中樞功能](event-hubs-features.md)。

本教學課程說明如何撰寫一個使用[事件處理器主機](event-hubs-event-processor-host.md)從事件中樞接收訊息的 .NET Core 主控台應用程式。 [事件處理器主機](event-hubs-event-processor-host.md)是一個 .NET 類別，透過管理持續檢查點以及來自事件中樞的平行接收，簡化來自事件中樞的事件接收作業。 使用事件處理器主機，可讓您將事件分割到多個接收者，即使裝載於不同的節點時也是一樣。 此範例說明單一接收者如何使用事件處理器主機。 [相應放大事件處理][使用事件中樞相應放大事件處理] 範例說明如何搭配使用事件處理器主機與多個接收者。

> [!NOTE]
> 您可以從 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) 下載此快速入門來作為範例，並以您事件中樞的值取代 `EventHubConnectionString`和 `EventHubName`、`StorageAccountName`、`StorageAccountKey` 和 `StorageContainerName` 字串，然後執行。 或者，您可以遵循本教學課程中的步驟，來建立自己的解決方案。

## <a name="prerequisites"></a>必要條件
* [Microsoft Visual Studio 2015 或 2017](https://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2017，但也支援 Visual Studio 2015。
* [.NET Core Visual Studio 2015 或 2017 工具](https://www.microsoft.com/net/core)。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>建立事件中樞命名空間和事件中樞
第一個步驟是使用 [Azure 入口網站](https://portal.azure.com)來建立「事件中樞」類型的命名空間，然後取得您應用程式與「事件中樞」進行通訊所需的管理認證。 若要建立命名空間和「事件中樞」，請依照[這篇文章](event-hubs-create.md)中的程序操作，然後繼續進行本教學課程中的下列步驟。

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>建立主控台應用程式

啟動 Visual Studio。 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。 建立 .NET Core 主控台應用程式。

![新增專案][2]

## <a name="add-the-event-hubs-nuget-package"></a>新增事件中樞 NuGet 封裝

遵循下列步驟，在您的專案中新增 [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 和 [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET Standard 程式庫 NuGet 套件： 

1. 以滑鼠右鍵按一下新建立的專案，然後選取 [管理 NuGet 套件]。
2. 按一下 [瀏覽] 索引標籤，然後搜尋 **Microsoft.Azure.EventHubs** 並選取 [Microsoft.Azure.EventHubs] 套件。 按一下 [安裝]  完成安裝作業，然後關閉此對話方塊。
3. 重複步驟 1 和 2，並安裝 **Microsoft.Azure.EventHubs.Processor** 套件。

## <a name="implement-the-ieventprocessor-interface"></a>實作 IEventProcessor 介面

1. 在 [方案總管] 中，於專案上按一下滑鼠右鍵，按一下 [新增]，然後按一下 [類別]。 將新類別命名為 **SimpleEventProcessor**。

2. 開啟 SimpleEventProcessor.cs 檔案，然後在檔案開頭新增下列 `using` 陳述式。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. 實作 `IEventProcessor` 介面。 以下列程式碼取代 `SimpleEventProcessor` 類別的整個內容：

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>使用 SimpleEventProcessor 更新 Main 方法

1. 在 Program.cs 檔案開頭處加入 `using` 陳述式。

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. 針對事件中樞連接字串、事件中樞名稱、儲存體帳戶容器名稱、儲存體帳戶名稱及儲存體帳戶金鑰，將常數新增到 `Program` 類別。 新增下列程式碼，其中將預留位置取代成其對應的值：

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 將名為 `MainAsync` 的新方法新增到 `Program` 類別，如下所示：

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. 將下列程式碼行新增至 `Main` 方法：

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 檔案看起來應該會像下面這樣：

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. 執行程式，並確定沒有任何錯誤。

恭喜！ 您現在已使用「事件處理器主機」從事件中樞接收訊息。

> [!NOTE]
> 本教學課程使用單一 [EventProcessorHost](event-hubs-event-processor-host.md)執行個體。 若要增加輸送量，建議您執行多個 [EventProcessorHost](event-hubs-event-processor-host.md) 執行個體 (如[相應放大事件處理](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3)範例所示)。 在這些情況下，多個執行個體會自動彼此協調，以對已接收的事件進行負載平衡。 

## <a name="next-steps"></a>後續步驟
在此快速入門中，您已建立可從事件中樞接收訊息的 .NET Core 應用程式。 若要了解如何使用 .NET Core 將事件傳送到事件中樞，請參閱[從事件中樞傳送事件 - .NET Core](event-hubs-dotnet-standard-getstarted-send.md)。

[1]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/event-hubs-python1.png
[2]: ./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png
