---
title: 透過 Azure 媒體服務 v3 使用 .NET 進行即時串流 | Microsoft Docs
description: 本教學課程將逐步解說透過媒體服務 v3 使用 .NET Core 進行即時串流的步驟。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: e4f32e14e8c1035055bd8a37bb453764984fbe4d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149126"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-net"></a>教學課程：透過媒體服務 v3 使用 .NET 進行即時串流

在 Azure 媒體服務中，[即時事件](https://docs.microsoft.com/rest/api/media/liveevents)會負責處理即時串流內容。 即時事件會提供輸入端點 (內嵌 URL)，接著您再提供給即時編碼器。 即時事件會從即時編碼器接收即時輸入資料流，再透過一或多個[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)進行串流處理。 即時事件也會提供預覽端點 (預覽 URL)，您可在進一步處理和傳遞之前先用來預覽及驗證您的資料流。 本教學課程說明如何使用 .NET Core 建立即時事件的**傳遞**類型。 

> [!NOTE]
> 請務必先檢閱[使用媒體服務 v3 進行即時串流](live-streaming-overview.md)，再繼續操作。 

本教學課程說明如何：    

> [!div class="checklist"]
> * 下載本主題中所述的範例應用程式
> * 檢查執行即時串流的程式碼
> * 使用 https://ampdemo.azureedge.net 上的 [Azure 媒體播放器](https://amp.azure.net/libs/amp/latest/docs/index.html)監看事件
> * 清除資源

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

需要有下列項目，才能完成教學課程。

- 安裝 Visual Studio Code 或 Visual Studio。
- [建立媒體服務帳戶](create-account-cli-how-to.md)。<br/>請務必記住您用於資源群組名稱和「媒體服務」帳戶名稱的值。
- 請依照[使用 Azure CLI 存取 Azure 媒體服務 API](access-api-cli-how-to.md) 中的步驟，並儲存認證。 您必須使用這些認證來存取 API。
- 用來廣播事件的相機或裝置 (例如筆記型電腦)。
- 內部部署即時編碼器，它會將相機中的訊號轉換成資料流，再傳送至媒體服務即時串流服務。 資料流的格式必須是 **RTMP** 或 **Smooth Streaming**。

## <a name="download-and-configure-the-sample"></a>下載並設定範例

使用以下命令將包含串流 .NET 範例的 GitHub 存放庫複製到您的機器：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

即時串流範例位於 [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) 資料夾中。

在您下載的專案中開啟 [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json)。 將值取代為從[存取 API](access-api-cli-how-to.md) 中取得的認證。

> [!IMPORTANT]
> 此範例會對每個資源使用唯一尾碼。 如果您取消偵錯，或在應用程式執行完成之前家以終止，則您的帳戶將會出現多個即時事件。 <br/>請確實停止執行即時事件。 否則將會產生相關**費用**！

## <a name="examine-the-code-that-performs-live-streaming"></a>檢查執行即時串流的程式碼

本節將檢查 *MediaV3LiveApp* 專案的 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) 檔案中所定義的函式。

此範例會為每個資源建立唯一的尾碼，因此，即使您執行此範例多次而未進行清除，也不會發生名稱衝突。

> [!IMPORTANT]
> 此範例會對每個資源使用唯一尾碼。 如果您取消偵錯，或在應用程式執行完成之前家以終止，則您的帳戶將會出現多個即時事件。 <br/>
> 請確實停止執行即時事件。 否則將會產生相關**費用**！
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>開始搭配使用媒體服務 API 與 .NET SDK

若要開始搭配使用媒體服務 API 與 .NET，您需要建立 **AzureMediaServicesClient** 物件。 若要建立物件，您需要提供必要的認證，讓用戶端使用 Azure AD 連線至 Azure。 在您於本文一開始複製的程式碼中，**GetCredentialsAsync** 函式會根據本機組態檔中提供的認證建立 ServiceClientCredentials 物件。 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>建立即時事件

本節說明如何建立**傳遞**類型的即時事件 (LiveEventEncodingType 設定為 [無])。 如需可用的即時事件類型詳細資訊，請參閱[即時事件類型](live-events-outputs-concept.md#live-event-types)。 
 
您可能想要在建立即時事件時指定下列各項：

* 媒體服務位置 
* 即時事件的串流通訊協定 (目前支援 RTMP 和 Smooth Streaming 通訊協定)。<br/>當「實況活動」或其相關「實況輸出」正在執行時，您無法變更通訊協定選項。 如果您需要不同的通訊協定，則應該為每個串流通訊協定建立個別的「實況活動」。  
* 內嵌和預覽的 IP 限制。 您可以定義獲允許將視訊內嵌到這個「實況活動」的 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如 ‘10.0.0.1’)、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1/22’)，或是使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 '10.0.0.1(255.255.252.0)')。<br/>如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。<br/>IP 位址必須採用下列其中一個格式：具有 4 個數字、CIDR 位址範圍的 IpV4 位址。
* 在建立事件時，您可以指定要自動加以啟動。 <br/>當自動啟動設為 true 時，即時事件將會在建立後隨即啟動。 這表示，在即時事件開始執行後就會馬上開始計費。 您必須在即時事件資源上明確呼叫「停止」，才能中止進一步計費。 如需詳細資訊，請參閱[即時事件狀態和計費](live-event-states-billing.md)。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>取得內嵌 URL

建立即時事件之後，您即可取得將提供給即時編碼器的內嵌 URL。 編碼器會使用這些 URL 來輸入即時串流。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>取得預覽 URL

使用 previewEndpoint 來預覽並確認已實際接收來自編碼器的輸入。

> [!IMPORTANT]
> 請先確定視訊流向預覽 URL，再繼續操作！

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>建立及管理即時事件與即時輸出

讓資料流流入即時事件之後，便可建立資產、即時輸出及串流定位器來開始串流事件。 這將封存串流，並透過「串流端點」將它提供給檢視器。 

#### <a name="create-an-asset"></a>建立資產

建立供即時輸出使用的資產。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>建立即時輸出

即時輸出會在建立時開始，並在刪除時結束。 當您刪除即時輸出時，您不是刪除基礎的資產和資產中的內容。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>建立串流定位器

> [!NOTE]
> 建立媒體服務帳戶時，**預設**串流端點會新增至 [已停止] 狀態的帳戶。 若要開始串流內容並利用[動態封裝](dynamic-packaging-overview.md)和動態加密功能，您想要串流內容的串流端點必須處於**執行中**狀態。 

當您已使用串流定位器發行即時輸出資產時，即時事件 (最長為 DVR 時段長度) 將繼續可檢視，直到串流定位器到期或遭到刪除，視孰者為早。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

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
* 停止即時事件。 即時事件在停止之後，就不會產生任何費用。 當您需要重新啟動它時，它會具有相同的內嵌 URL，因此您不需要重新設定編碼器。
* 除非您想要繼續將即時事件封存為隨選串流，否則您可以停止「串流端點」。 如果即時事件處於已停止狀態，就不會產生任何費用。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

下列程式碼會示範如何清理所有即時事件的帳戶：

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>監看事件

若要監看事件，請複製您根據「建立串流定位器」中所述來執行程式碼時取得的串流 URL，並使用您所選擇的播放器。 您可以使用 https://ampdemo.azureedge.net 上的 [Azure 媒體播放器](https://amp.azure.net/libs/amp/latest/docs/index.html)來測試您的資料流。 

即時事件會在停止時將事件自動轉換為點播內容。 只要您未刪除資產，即使在停止並刪除事件之後，使用者還是可以視需求將封存的內容串流為視訊。 如果事件使用資產，則無法刪除資產；必須先刪除事件。 

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包含您為教學課程建立的媒體服務和儲存體帳戶)，請將稍早建立的資源群組刪除。

執行下列 CLI 命令：

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> 讓即時事件處於執行狀態將會產生費用。 請注意，如果專案/程式失效或因故關閉，即時事件可能會在計費狀態下持續執行。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[串流檔案](stream-files-tutorial-with-api.md)
 
