---
title: 透過 Azure 媒體服務 v3 使用 .NET Core 進行即時串流 | Microsoft Docs
description: 本教學課程將逐步解說透過媒體服務 v3 使用 .NET Core 進行即時串流的步驟。
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: 82ef04993b597778c808d649032603a0f3672e4c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110325"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>透過 Azure 媒體服務 v3 使用 .NET Core 進行即時串流

在媒體服務中，[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 會負責處理即時串流內容。 LiveEvent 會提供輸入端點 (內嵌 URL)，接著您再提供給即時編碼器。 LiveEvent 會從即時編碼器接收即時輸入資料流，再透過一或多個 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 進行串流處理。 LiveEvent 也會提供預覽端點 (預覽 URL)，您可在進一步處理和傳遞之前先用來預覽及驗證您的資料流。 本教學課程說明如何使用 .NET Core 建立即時事件的**傳遞**類型。 

> [!NOTE]
> 請務必先檢閱[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)，再繼續操作。 

本教學課程說明如何：    

> [!div class="checklist"]
> * 建立媒體服務帳戶
> * 存取媒體服務 API
> * 設定範例應用程式
> * 檢查執行即時串流的程式碼
> * 使用 http://ampdemo.azureedge.net 上的 [Azure 媒體播放器](http://amp.azure.net/libs/amp/latest/docs/index.html)監看事件
> * 清除資源

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先決條件

需要有下列項目，才能完成教學課程。

* 安裝 Visual Studio Code 或 Visual Studio
* 用來廣播事件的相機或裝置 (例如筆記型電腦)。
* 內部部署即時編碼器，它會將相機中的訊號轉換成資料流，再傳送至媒體服務即時串流服務。 資料流的格式必須是 **RTMP** 或 **Smooth Streaming**。

## <a name="download-the-sample"></a>下載範例

使用以下命令將包含串流 .NET 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

即時串流範例位於 [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) 資料夾中。

> [!IMPORTANT]
> 此範例會對每個資源使用唯一尾碼。 如果您取消偵錯，或在應用程式執行完成之前家以終止，則您的帳戶將會出現多個 LiveEvent。 <br/>
> 請確實停止執行 LiveEvent。 否則將會產生相關**費用**！

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>檢查執行即時串流的程式碼

本節將檢查 *MediaV3LiveApp* 專案的 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) 檔案中所定義的函式。

此範例會為每個資源建立唯一的尾碼，因此，即使您執行此範例多次而未進行清除，也不會發生名稱衝突。

> [!IMPORTANT]
> 此範例會對每個資源使用唯一尾碼。 如果您取消偵錯，或在應用程式執行完成之前家以終止，則您的帳戶將會出現多個 LiveEvent。 <br/>
> 請確實停止執行 LiveEvent。 否則將會產生相關**費用**！
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>開始搭配使用媒體服務 API 與 .NET SDK

若要開始搭配使用媒體服務 API 與 .NET，您需要建立 **AzureMediaServicesClient** 物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 在您於本文一開始複製的程式碼中，**GetCredentialsAsync** 函式會根據本機組態檔中提供的認證建立 ServiceClientCredentials 物件。  

```csharp
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>建立即時事件

本節說明如何建立**傳遞**類型的 LiveEvent (LiveEventEncodingType 設定為 [無])。 如果您想要建立為即時編碼啟用的 LiveEvent，請將 LiveEventEncodingType 設定為 [基本]。 

您在建立即時事件時也可以進行下列指定：

* 媒體服務位置 
* 即時事件的串流通訊協定 (目前支援 RTMP 和 Smooth Streaming 通訊協定)
       
    當 LiveEvent 或其相關聯的 LiveOutput 正在執行時，您無法變更通訊協定選項。 如果您需要不同的通訊協定，則應該為每個串流通訊協定建立個別的 LiveEvent。  
* 內嵌和預覽的 IP 限制。 您可以定義允許將視訊內嵌到這個 LiveEvent 的 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如 ‘10.0.0.1’)、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1/22’)，或是使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 '10.0.0.1(255.255.252.0)')。
    
    如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。

在建立事件時，您可以指定要自動加以啟動。 

```csharp
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                new IPRange (
                    name: "AllowAll",
                    address: "0.0.0.0",
                    subnetPrefixLength: 0
                )
            }
        )
    )
};

// This can sometimes take awhile. Be patient.
LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location, 
    description:"Sample LiveEvent for testing",
    vanityUrl:false,
    encoding: new LiveEventEncoding(
                // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                encodingType:LiveEventEncodingType.None, 
                presetName:null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP), 
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency.
        // Low latency reduces the amount of buffering Media Services does.
        // Low latency can also reduce the stability of the live stream. 
        StreamOptionsFlag.Default
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");
liveEvent = client.LiveEvents.Create(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart:true);
```

### <a name="get-ingest-urls"></a>取得內嵌 URL

建立通道之後，即可取得您提供給即時編碼器的內嵌 URL。 編碼器會使用這些 URL 來輸入即時串流。


```csharp
// Get the input endpoint to configure the on-premises encoder with
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on-premises encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>取得預覽 URL

使用 previewEndpoint 來預覽並確認已實際接收來自編碼器的輸入。

> [!IMPORTANT]
> 請先確定視訊流向預覽 URL，再繼續操作！

```sharp
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}");
Console.WriteLine();
```

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>建立及管理 LiveEvent 和 LiveOutput

讓資料流流入 LiveEvent 之後，您即可建立「資產」、LiveOutput 和 StreamingLocator 開始串流事件。 這會封存資料流，並透過 StreamingEndpoint 將它提供給檢視器。 

#### <a name="create-an-asset"></a>建立資產

建立供 LiveOutput 使用的資產。

```csharp
string assetName = "archiveAsset" + uniqueness;
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = client.Assets.CreateOrUpdate(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-liveoutput"></a>建立 LiveOutput

```csharp
string manifestName = "output";
string liveOutputName = "liveOutput" + uniqueness;
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = client.LiveOutputs.Create(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaminglocator"></a>建立 StreamingLocator

> [!NOTE]
> 建立媒體服務帳戶時，**預設**串流端點會新增至 [已停止] 狀態的帳戶。 若要開始串流內容並利用動態封裝和動態加密功能，您想要串流內容的串流端點必須處於 [執行中] 狀態。 


```csharp
StreamingLocator locator = new StreamingLocator(assetName: assetName, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = client.StreamingLocators.Create(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(config.ResourceGroup, config.AccountName, "default");

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    client.StreamingEndpoints.Start(config.ResourceGroup, config.AccountName, "default");
}

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>清除媒體服務帳戶中的資源

如果您完成串流處理事件，而且想要清除先前佈建的資源，請遵循下列程序。

* 停止從編碼器發送串流。
* 停止 LiveEvent。 LiveEvent 在停止之後，就不會產生任何費用。 當您需要重新啟動它時，它會具有相同的內嵌 URL，因此您不需要重新設定編碼器。
* 除非您想要繼續將即時事件封存為隨選資料流，否則您可以停止 StreamingEndpoint。 如果 LiveEvent 處於已停止狀態，就不會產生任何費用。

```csharp
private static void CleanupLiveEventAndOutput(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName, string liveOutputName)
{
    // Delete the LiveOutput
    client.LiveOutputs.Delete(resourceGroup, accountName, liveEventName, liveOutputName);

    // Stop and delete the LiveEvent
    client.LiveEvents.Stop(resourceGroup, accountName, liveEventName);
    client.LiveEvents.Delete(resourceGroup, accountName, liveEventName);
}

private static void CleanupLocatorAssetAndStreamingEndpoint(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    // Delete the Streaming Locator
    client.StreamingLocators.Delete(resourceGroup, accountName, streamingLocatorName);

    // Delete the Archive Asset
    client.Assets.Delete(resourceGroup, accountName, assetName);
}

private static void CleanupAccount(IAzureMediaServicesClient client, string resourceGroup, string accountName)
{
    try{
        Console.WriteLine("Cleaning up the resources used, stopping the LiveEvent. This can take a few minutes to complete.");
        Console.WriteLine();

        var events = client.LiveEvents.List(resourceGroup, accountName);
        
        foreach (LiveEvent l in events)
        {
            if (l.Name == liveEventName){
                var outputs = client.LiveOutputs.List(resourceGroup, accountName, l.Name);

                foreach (LiveOutput o in outputs)
                {
                    client.LiveOutputs.Delete(resourceGroup, accountName, l.Name, o.Name);
                    Console.WriteLine($"LiveOutput: {o.Name} deleted from LiveEvent {l.Name}. The archived Asset and Streaming URLs are still retained for on-demand viewing.");
                }

                if (l.ResourceState == LiveEventResourceState.Running){
                    client.LiveEvents.Stop(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Stopped.");
                    client.LiveEvents.Delete(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Deleted.");
                    Console.WriteLine();
                }
            }
        }
    } 
    catch(ApiErrorException e)
    {
        Console.WriteLine("Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();

    }
}
```        

## <a name="watch-the-event"></a>監看事件

若要監看事件，請複製您在執行[建立 StreamingLocator](#create-a-streaminglocator) 中說明的程式碼時所取得的串流 URL，並使用您所選擇的播放器。 您可以使用 http://ampdemo.azureedge.net 上的 [Azure 媒體播放器](http://amp.azure.net/libs/amp/latest/docs/index.html)來測試您的資料流。 

即時事件會在停止時將事件自動轉換為點播內容。 只要您未刪除資產，即使在停止並刪除事件之後，使用者還是可以視需求將封存的內容串流為視訊。 如果事件使用資產，則無法刪除資產；必須先刪除事件。 

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包含您為教學課程建立的媒體服務和儲存體帳戶)，請將稍早建立的資源群組刪除。 您可以使用 [CloudShell] 工具。

在 **CloudShell** 中，執行以下命令：

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> 讓 LiveEvent 處於執行狀態將會產生費用。 請注意，如果專案/程式失效或因故關閉，LiveEvent 可能會在計費狀態下持續執行。

## <a name="next-steps"></a>後續步驟

[串流檔案](stream-files-tutorial-with-api.md)

