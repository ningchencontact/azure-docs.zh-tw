---
title: 記錄與診斷 Azure 空間的錨點功能 |Microsoft Docs
description: 深入說明如何產生及擷取記錄和診斷 Azure 空間的錨點中。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882823"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>記錄與診斷 Azure 空間的錨點功能

Azure 空間的錨點會提供適用於應用程式開發的標準記錄機制。 空間的錨點的診斷記錄模式時，您需要進行偵錯的詳細資訊。 診斷記錄會儲存環境的映像。

## <a name="standard-logging"></a>標準記錄
在空間的錨點 API 中，您可以訂閱的記錄機制，以取得應用程式開發和偵錯很有用的記錄。 標準記錄 Api 不會儲存環境的圖片裝置磁碟上。 SDK 會提供這些記錄檔，做為事件回呼。 它要將這些記錄檔整合到應用程式的記錄機制。

### <a name="configuration-of-log-messages"></a>記錄檔訊息的組態
有兩個回呼的使用者感興趣。 下列範例示範如何將工作階段設定。

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>事件和屬性

這些事件回呼會提供處理記錄檔和錯誤都會從工作階段：

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel):指定要接收事件，從執行階段的詳細程度。
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug):提供標準的偵錯記錄檔事件。
- [錯誤](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)：提供執行階段視為錯誤的記錄事件。

## <a name="diagnostics-logging"></a>診斷記錄

除了標準模式的作業記錄中，空間的錨點也會有診斷模式。 診斷模式會擷取環境的映像，並加以記錄至磁碟。 您可以使用此模式中偵錯特定種類的問題，例如透過可預測方式找不到錨點。 啟用診斷記錄，只以重現特定問題。 然後停用它。 通常執行您的應用程式時，請勿啟用診斷。

支援互動期間與 Microsoft，Microsoft 代表可能會詢問您是否願意送出診斷組合，以進一步調查。 在此情況下，您可能會決定要啟用診斷，並重現問題，因此您可以送出診斷的套件組合。 

如果您的診斷記錄檔向 Microsoft 提交沒有先前的通知 Microsoft 代表，提交會變成未回應。

下列各節說明如何啟用診斷模式以及如何提交給 Microsoft 的診斷記錄檔。

### <a name="enable-diagnostics-logging"></a>啟用診斷記錄

當您啟用診斷記錄的工作階段時，工作階段中的所有作業都有對應的診斷記錄本機檔案系統中。 在記錄期間環境影像會儲存到磁碟。

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>送出診斷套件組合

下列程式碼片段示範如何提交給 Microsoft 的診斷組合。 此搭售方案將會包含環境之後啟用診斷工作階段所擷取的映像。 

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>診斷套件組合的組件
診斷套件組合可能包含下列資訊：

- **主要畫面格影像**:已啟用 [診斷] 時，工作階段期間擷取環境的映像。
- **記錄**：執行階段所記錄的記錄事件。
- **工作階段中繼資料**:識別工作階段的中繼資料。
