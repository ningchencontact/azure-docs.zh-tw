---
title: "讓應用程式資料在 Azure 中具有高可用性 | Microsoft Docs"
description: "使用讀取權限異地備援儲存體讓應用程式資料具有高可用性"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 63ca91c2eadf7b003427e9716d99621fca1b1a19
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>使用 Azure 儲存體讓應用程式資料具有高可用性

本教學課程是一個系列的第一部分。 本教學課程會說明如何讓應用程式資料在 Azure 中具有高可用性。 當您完成時，您有.NET core 主控台應用程式上傳和擷取 blob[讀取存取異地備援](../common/storage-redundancy.md#read-access-geo-redundant-storage)(RA-GRS) 儲存體帳戶。 RA-GRS 的運作方式是將交易從主要區域複寫到次要區域。 此複寫程序可保證次要區域中的資料是最終一致的。 該應用程式會使用[斷路器](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker)模式來決定要連線到哪一個端點。 在模擬失敗狀況時，應用程式會切換到次要端點。

在系列的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 建立儲存體帳戶
> * 下載範例
> * 設定連接字串
> * 執行主控台應用程式

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

* 使用下列工作負載安裝 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
  - **Azure 開發**

  ![Azure 開發 (在 [Web 和 Cloud] 之下)](media/storage-create-geo-redundant-storage/workloads.png)

* [下載並安裝 Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-storage-account"></a>建立儲存體帳戶

儲存體帳戶提供唯一命名空間來儲存及存取您的 Azure 儲存體資料物件。

請遵循下列步驟來建立讀取權限異地備援儲存體帳戶：

1. 選取 Azure 入口網站左上角的 [新增] 按鈕。

2. 選取 [新增] 頁面上的 [儲存體]，然後選取 [精選] 下的 [儲存體帳戶 - Blob、檔案、資料表、佇列]。
3. 在儲存體帳戶表單中填寫下列資訊 (如下圖所示)，然後選取 [建立]：

   | 設定       | 建議的值 | 說明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **名稱** | mystorageaccount | 用於儲存體帳戶的唯一值 |
   | **部署模型** | Resource Manager  | Resource Manager 含有最新的功能。|
   | **帳戶類型** | 一般用途 | 如需帳戶類型的詳細資訊，請參閱[儲存體帳戶的類型](../common/storage-introduction.md#types-of-storage-accounts) |
   | **效能** | 標準 | 標準便足供此案例範例使用。 |
   | **複寫**| 讀取權限異地備援儲存體 (RA-GRS) | 這是讓範例起作用的必要項目。 |
   |**需要安全傳輸** | 已停用| 此案例不需要安全傳輸。 |
   |**訂用帳戶** | 您的訂用帳戶 |如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   |**ResourceGroup** | myResourceGroup |如需有效的資源群組名稱，請參閱[命名規則和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。 |
   |<bpt id="p1">**</bpt>Location<ept id="p1">**</ept> | 美國東部 | 選擇位置。 |

![建立儲存體帳戶](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>下載範例

[下載範例專案](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)。

擷取 (解壓縮) storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip 檔案。
此範例專案包含一個主控台應用程式。

## <a name="set-the-connection-string"></a>設定連接字串

在應用程式，您必須提供儲存體帳戶的連接字串。 建議您，此連接字串儲存在環境變數執行應用程式的本機電腦上。 請遵循以下的範例，根據您的作業系統建立環境變數的其中一個。

在 Azure 入口網站中，瀏覽至儲存體帳戶。 選取**存取金鑰**下**設定**儲存體帳戶中。 複製**連接字串**從主要或次要金鑰。 取代\<yourconnectionstring\>實際連線字串，執行下列命令的其中一個會根據您的作業系統。 此命令會將本機電腦中的環境變數。 在 Windows 中，環境變數不能使用直到重新載入**命令提示字元**或您使用的殼層。 取代 **\<storageConnectionString\>** 在下列範例中：

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

![應用程式組態檔](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>執行主控台應用程式

在 Visual Studio 中，按下 **F5** 或選取 [啟動]，開始對應用程式進行偵錯。 Visual studio 會自動還原遺漏的 NuGet 封裝如果設定，請造訪[安裝與解除安裝之封裝的封裝還原](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview)如需詳細資訊。

隨即會有主控台視窗啟動，應用程式也會開始執行。 此應用程式會將 **HelloWorld.png** 影像從解決方案上傳到儲存體帳戶。 此應用程式會進行檢查，以確保影像已複寫到次要 RA-GRS 端點。 之後，則會開始下載影像，最多會下載 999 次。 每個讀取由**P**或**S**。其中，**P** 代表主要端點，**S** 則代表次要端點。

![主控台應用程式執行中](media/storage-create-geo-redundant-storage/figure3.png)

在程式碼範例中，系統會使用 `Program.cs` 檔案中的 `RunCircuitBreakerAsync` 工作，透過 [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet) 方法從儲存體帳戶下載影像。 在下載之前，系統會先定義 [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet)。 作業內容會定義事件處理常式，當下載作業成功完成，或下載作業失敗而正在重試時，便會引發這些處理常式。

### <a name="retry-event-handler"></a>重試事件處理常式

`OperationContextRetrying`當映像下載失敗且重試設定時，呼叫事件處理常式。 如果到達最大數目的應用程式中定義的重試次數， [LocationMode](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode)要求的變更為`SecondaryOnly`。 這項設定會迫使應用程式嘗試從次要端點下載影像。 因為不會無限期地重試主要端點，這個設定可減少要求該影像所花費的時間。

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>要求已完成的事件處理常式

當影像下載成功時，便會呼叫 `OperationContextRequestCompleted` 事件處理常式。 如果應用程式使用次要端點，則應用程式會繼續使用此端點，但最多 20 次。 應用程式設定的 20 倍之後, [LocationMode](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode)回到`PrimaryThenSecondary`和重試主要端點。 如果要求不成功，應用程式會繼續從主要端點讀取。

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

在系列的第一部分中，您已了解如何使用 RA-GRS 儲存體帳戶讓應用程式具有高可用性，例如如何：

> [!div class="checklist"]
> * 建立儲存體帳戶
> * 下載範例
> * 設定連接字串
> * 執行主控台應用程式

請進入系列的第二個部分，以了解如何模擬失敗狀況，並強制應用程式使用次要 RA-GRS 端點。

> [!div class="nextstepaction"]
> [模擬對主要儲存體端點的連線失敗](storage-simulate-failure-ragrs-account-app.md)
