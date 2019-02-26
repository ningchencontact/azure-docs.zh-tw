---
title: 使用 Azure 媒體服務串流視訊檔案 - CLI | Microsoft Docs
description: 按照本快速入門的步驟建立新的 Azure 媒體服務帳戶、對檔案進行編碼，然後將它串流到 Azure 媒體播放器。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: Azure 媒體服務, 串流
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: ''
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: 8de004b0ca55cb46336a072dabb682f342c7d8dd
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446489"
---
# <a name="quickstart-stream-video-files---cli"></a>快速入門：串流影片檔案 - CLI

此快速入門說明使用 Azure 媒體服務在各種不同的瀏覽器和裝置上編碼和開始串流視訊是多麼容易。 您可以使用 HTTP(S) URL、SAS URL 或 Azure Blob 儲存體中的檔案路徑來指定輸入內容。
此主題中的範例會將您透過 HTTPS URL 提供的內容編碼。 目前，AMS v3 不支援透過 HTTPS URL 的區塊傳送編碼。

完成快速入門時，您將能夠串流視訊。  

![播放影片](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>建立媒體服務帳戶

若要在 Azure 中開始加密、編碼、分析、管理和串流處理媒體內容，您需要建立 Media Services 帳戶。 媒體服務帳戶必須與一或多個儲存體帳戶產生關聯。

媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 強烈建議使用與媒體服務帳戶位於相同位置的儲存體帳戶，以避免產生額外的延遲和資料輸出費用。

### <a name="create-a-resource-group"></a>建立資源群組

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

在此範例中，我們將建立一般用途 v2 的標準 LRS 帳戶。

如果您想要以儲存體帳戶進行試驗，請使用 `--sku Standard_LRS`。 不過，在選擇用於生產環境的 SKU 時應考慮使用 `--sku Standard_RAGRS`，以提供地理複寫功能而確保商務持續性。 如需詳細資訊，請參閱[儲存體帳戶](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)。
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-service-account"></a>建立 Azure 媒體服務帳戶

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

您會看到如下的回應：

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-streaming-endpoint"></a>啟動串流端點

下列 CLI 會啟動預設**串流端點**。

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

啟動之後，您會看到如下的回應：

```
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

如果串流端點已在執行中，您會看到

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>建立調適性位元速率編碼的轉換

建立**轉換**，以設定視訊編碼或分析的一般工作。 在此範例中，我們想要執行調適性位元速率編碼。 然後，您將在已建立的轉換下提交**作業**。 作業是要媒體服務將轉換套用至指定輸入視訊或音訊內容的實際要求。

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

您會看到如下的回應：

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>建立輸出資產

建立輸出**資產**，以作為編碼作業的輸出。

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

您會看到如下的回應：

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-job-with-https-input"></a>啟動使用 HTTPS 輸入的作業

在媒體服務 v3 中，當您提交工作來處理視訊時，必須告知媒體服務到何處尋找輸入視訊。 其中一個選項是將 HTTPS URL 指定為作業輸入 (如本範例所示)。 

執行 `az ams job start` 時，您可以設定作業輸出的標籤。 這個標籤後續可用來識別此輸出資產的用途。 

- 如果您為標籤指派值，請將 ‘--output-assets’ 設為 “assetname=label”
- 若未將值指派給標籤，請將 ‘--output-assets’ 設為 “assetname=”。
  請注意，您為 `output-assets` 新增了 "="。 

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

您會看到如下的回應：

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>檢查狀態

在 5 分鐘後檢查作業的狀態。 其狀態應為「完成」。 如果不是，請過幾分鐘再檢查。 狀態為「完成」後，請移至下一個步驟，並建立**串流定位器**。

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-streaming-locator-and-get-path"></a>建立串流定位器並取得路徑

編碼完成後，下一個步驟是要讓用戶端可播放輸出資產中的視訊。 您可以透過兩個步驟來執行此動作：第一步，建立**串流定位器**，第二步，建置用戶端可以使用的串流 URL。

### <a name="create-a-streaming-locator"></a>建立串流定位器

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

您會看到如下的回應：

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>取得串流定位器路徑

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

您會看到如下的回應：

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

複製 Hls 路徑。 在此案例中：`/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`。

## <a name="build-url"></a>組建 URL 

### <a name="get-streaming-endpoint-host-name"></a>取得串流端點主機名稱

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

複製 `hostName` 值。 在此案例中：`amsaccount-usw22.streaming.media.azure.net`。

### <a name="assemble-url"></a>組合 URL

"https:// " + &lt;hostName 值&gt; + &lt;Hls 路徑值&gt;

#### <a name="example"></a>範例

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-with-azure-media-player"></a>使用 Azure 媒體播放器來測試播放

本文使用 Azure 媒體播放器測試串流。 

> [!NOTE]
> 如果播放程式裝載在 HTTPS 網站上，請務必將 URL 更新為 "https"。

1. 開啟瀏覽器並巡覽至 [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)。
2. 在 [URL:] 方塊中，貼上您在上一節建置的 URL。 

  您可以貼上 HLS、Dash 或 Smooth 格式的 URL，Azure 媒體播放器將會切換至適當的串流通訊協定，以便在您的裝置上自動播放。
3. 按一下 [更新播放程式]。

Azure 媒體播放器可以用於測試，但不應用於生產環境。 

## <a name="clean-up-resources"></a>清除資源

如果您不再需要資源群組中的任何資源 (包含在此快速入門中建立的媒體服務和儲存體帳戶)，請將資源群組刪除。

執行下列 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>另請參閱

請參閱[作業錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [CLI 範例](cli-samples.md)
