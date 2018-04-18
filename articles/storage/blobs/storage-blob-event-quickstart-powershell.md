---
title: 將 Azure Blob 儲存體事件路由至自訂 Web 端點 - PowerShell | Microsoft Docs
description: 使用 Azure Event Grid 以訂閱 Blob 儲存體事件。
services: storage,event-grid
keywords: ''
author: david-stanford
ms.author: dastanfo
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: e5524732185d7b80ebf16a9bce6de9ca0183c27e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-powershell"></a>使用 PowerShell 將 Blob 儲存體事件路由至自訂的 Web 端點

Azure Event Grid 是一項雲端事件服務。 在本文中，您可以使用 Azure PowerShell 來訂閱 Blob 儲存體事件、觸發事件並檢視結果。 

一般而言，您可將事件傳送至可回應事件的端點，例如 Webhook 或 Azure Function。 為了簡化本文中所示的範例，會將事件傳送至只收集訊息的 URL。 使用 [Hookbin](https://hookbin.com/) 提供的第三方工具來建立此 URL。

> [!NOTE]
> **Hookbin** 不適用於高輸送量的用途。 此工具單純用於示範。 如果您一次推送多個事件，則可能看不到工具中的所有事件。

當您完成本文所述的步驟時，您會看到事件資料已傳送至端點。

## <a name="setup"></a>設定

本文需要您執行最新版本的 Azure PowerShell。 如果您需要安裝或升級，請參閱[安裝和設定 Azure PowerShell](/powershell/azure/install-azurerm-ps)。

## <a name="log-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶，並遵循畫面上的指示以進行驗證。

```powershell
Login-AzureRmAccount
```

> [!NOTE]
> 儲存體事件的可用性會繫結至事件格線[可用性](../../event-grid/overview.md)，並且將在其他區域中變成可用狀態，就像事件格線所做的一樣。

這個範例會使用 **westus2**，並將選取項目儲存在變數中，以利隨時使用。

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>建立資源群組

Event Grid 為 Azure 資源，必須放入 Azure 資源群組中。 資源群組是在其中部署與管理 Azure 資源的邏輯集合。

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令建立資源群組。

下列範例會在 westus2 位置建立名為 gridResourceGroup 的資源群組。  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶

若要使用 Blob 儲存體事件，您需要 [Blob 儲存體帳戶](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts)或[一般用途 v2 儲存體帳戶](../common/storage-account-options.md#general-purpose-v2)。 **一般用途 v2 (GPv2)** 儲存體帳戶支援所有儲存體服務 (包括 Blob、檔案、佇列和表格) 的所有功能。 **Blob 儲存體帳戶**是特製化的儲存體帳戶，可將非結構化資料儲存為 Azure 儲存體中的 Blob (物件)。 Blob 儲存體帳戶類似於一般用途儲存體帳戶，可共用所有強大的持續性、可用性、延展性以及您現今使用的效能功能，包括區塊 Blob 和附加 Blob 的 100% API 一致性。 對於只需要封鎖或附加 Blob 儲存體的應用程式，我們建議使用 Blob 儲存體帳戶。  

使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) 建立可進行 LRS 複寫的 Blob 儲存體帳戶，然後擷取儲存體帳戶內容，此內容定義要使用的儲存體帳戶。 使用儲存體帳戶時，會參考內容而非重複提供認證。 這個範例會建立名為 **gridstorage** 的儲存體帳戶，並含有本機備援儲存體 (LRS)。 

> [!NOTE]
> 儲存體帳戶名稱位於全域命名空間中，因此，您需要將某些隨機字元附加至此指令碼中提供的名稱。

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>建立訊息端點

訂閱主題之前，讓我們建立事件訊息的端點。 讓我們建立可收集訊息的端點，以便檢視訊息，而不需撰寫程式碼來回應事件。 Hookbin 是一個第三方工具，可讓您建立端點，以及檢視傳送給它的要求。 移至 [Hookbin](https://hookbin.com/) 並按一下 [建立新的端點]。 在下列指令碼中，複製 Bin URL 並取代 `<bin URL>`。

```powershell
$binEndPoint = "<bin URL>"
```

## <a name="subscribe-to-your-storage-account"></a>訂閱您的儲存體帳戶

您可訂閱主題，告知 Event Grid 您想要追蹤的事件。下列範例會訂閱您所建立的儲存體帳戶，並從 Hookbin 傳遞 URL 作為事件通知的端點。 

```powershell
$storageId = (Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
New-AzureRmEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $binEndPoint `
  -ResourceId $storageId
```

## <a name="trigger-an-event-from-blob-storage"></a>從 Blob 儲存體觸發事件

現在，讓我們觸發事件以了解 Event Grid 如何將訊息散發至您的端點。 首先，建立容器和物件。 然後，將物件上傳至容器。

```powershell
$containerName = "gridcontainer"
New-AzureStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzureStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

您已觸發此事件，而 Event Grid 會將訊息傳送至您在訂閱時設定的端點。 瀏覽至您稍早建立的端點 URL。 或者，按一下已開啟瀏覽器中的重新整理。 您會看到剛傳送的事件。 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>清除資源
如果您打算繼續使用此儲存體帳戶和事件訂用帳戶，請勿清除在本文中建立的資源。 如果您不打算繼續，請使用下列命令來刪除您在本文建立的資源。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

您現在知道如何建立主題和事件訂用帳戶，請深入了解 Blob 儲存體事件以及 Event Grid 如何協助您：

- [回應 Blob 儲存體事件](storage-blob-event-overview.md)
- [關於 Event Grid](../../event-grid/overview.md)
