---
title: 將區塊 Blob 儲存在裝置上 - Azure IoT Edge | Microsoft Docs
description: 了解階層處理和存留時間的功能、 查看支援的 blob 儲存體作業，並連接到您的 blob 儲存體帳戶。
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f70ca550f1688551abb94bb30ba4f76eb3c36404
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303972"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>在 IoT Edge (預覽) 使用 Azure Blob 儲存體，以便在邊緣儲存資料

IoT Edge 上的 Azure Blob 儲存體提供邊緣的[區塊 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 儲存體解決方案。 IoT Edge 裝置上的 Blob 儲存體模組如同 Azure 區塊 Blob 服務般運作，但是區塊 Blob 會在本機 IoT Edge 裝置上儲存。 您可以使用平常使用的相同 Azure 儲存體 SDK 方法或區塊 Blob API 呼叫存取您的 Blob。

此模組隨附**deviceToCloudUpload**並**deviceAutoDelete**功能。
> [!NOTE]
> IoT Edge 上的 Azure Blob 儲存體屬於[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

觀看快速簡介影片
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload**是可設定的功能，可讓您自動將資料上傳從您的本機 blob 儲存體 azure 具有間歇性網際網路連接性支援。 该功能允许：

- 開啟 on/off deviceToCloudUpload 功能。
- 選擇的資料複製到 Azure，例如 NewestFirst 或 OldestFirst 的順序。
- 指定要将数据上传到的 Azure 存储帐户。
- 指定要上传到 Azure 的容器。 此模块允许指定源和目标容器名称。
- 選擇能夠上的傳至雲端儲存體完成之後，立即刪除 blob
- 執行完整的 blob 上傳 (使用`Put Blob`作業) 和區塊層級的上傳 (使用`Put Block`和`Put Block List`作業)。

當您的 blob 是由區塊所組成，此模組會使用區塊層級上傳。 下面是一些常见场景：

- 您的應用程式會更新某些先前上傳 blob 的區塊，此模組會將上傳更新的區塊並不完整的 blob。
- 此模組將 blob 上傳和網際網路連線中斷，連線時回到一次只剩下的區塊並不完整的 blob 上傳。

如果在 Blob 上传期间发生意外的进程终止（例如电源故障），当模块重新联机时，将再次上传需要上传的所有块。

**deviceAutoDelete**是可設定的功能，模組會自動刪除您的 blob 從本機儲存體時指定的持續時間 （以分鐘為單位） 到期。 该功能允许：

- 開啟 on/off deviceAutoDelete 功能。
- 指定以分鐘為單位 (deleteAfterMinutes) 的時間之後的 blob 將會自動刪除。
- 選擇讓它上傳時如果 deleteAfterMinutes 值過期保留 blob。

对于需要在本地存储视频、图像、金融数据、医院数据或其他任何数据，以后在本地处理数据或将其传输到云的场合，都很适合使用此模块。

這篇文章說明您的 IoT Edge 裝置執行的 blob 服務的 IoT Edge 容器上的 Azure Blob 儲存體相關的概念。

## <a name="prerequisites"></a>必要條件

Azure IoT Edge 裝置：

- 您可以使用您的開發電腦或虛擬機器為 IoT Edge 裝置的快速入門中的步驟[Linux](quickstart-linux.md)或是[Windows 裝置](quickstart.md)。

- IoT Edge 模組上的 Azure Blob 儲存體支援下列裝置設定：

  | 作業系統 | 架構 |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Enterprise | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian-stretch | ARM32 |

雲端資源：

Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 和 deviceAutoDelete 屬性

若要設定 deviceToCloudUploadProperties 和 deviceAutoDeleteProperties 使用所需的屬性。 可以在部署期间设置这些属性，或者，以后可以通过编辑模块孪生来更改这些属性，而无需重新部署。 我们建议检查“模块孪生”中的 `reported configuration` 和 `configurationValidation`，以确保正确传播值。

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

此设置的名称为 `deviceToCloudUploadProperties`

| 欄位 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| uploadOn | true、false | 默认设置为 `false`，若要启用它，可将它设置为 `true`|
| uploadOrder | NewestFirst、OldestFirst | 可讓您選擇的資料複製到 Azure 的順序。 默认设置为 `OldestFirst`。 顺序由 Blob 的上次修改时间确定 |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 是一个连接字符串，用于指定要将数据上传到的 Azure 存储帐户。 指定 `Azure Storage Account Name`、`Azure Storage Account Key` 或 `End point suffix`。 新增適當 EndpointSuffix 的 Azure 位置將上傳資料，而不同的全域 Azure Government Azure，Microsoft Azure Stack。 |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 用于指定要上传到 Azure 的容器名称。 此模块允许指定源和目标容器名称。 如果未指定目标容器名称，系统会自动分配 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` 作为容器名称。 您可以建立範本字串中的目標容器名稱，查看可能的值資料行。 <br>* %h -> IoT 中心名称（3 到 50 个字符）。 <br>* %d]-> [IoT Edge 裝置識別碼 (1 到 129 個字元)。 <br>* %m -> 模块名称（1 到 64 个字符）。 <br>* %c -> 源容器名称（3 到 63 个字符）。 <br><br>容器名稱的大小上限是 63 個字元，而自動指派的目標容器名稱如果容器的大小超過 63 個字元，它會修剪 （IoTHubName、 IotEdgeDeviceID、 模組名稱、 SourceContainerName） 每個區段為 15字元。 |
| deleteAfterUpload | true、false | 默认设置为 `false`。 當設定為`true`，完成上的傳至雲端儲存體時，它會自動刪除資料 |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

此设置的名称为 `deviceAutoDeleteProperties`

| 欄位 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| deleteOn | true、false | 默认设置为 `false`，若要启用它，可将它设置为 `true`|
| deleteAfterMinutes | `<minutes>` | 指定以分鐘為單位的時間。 此模組會自動刪除您的 blob 從本機儲存體時這個值過期 |
| retainWhileUploading | true、false | 依預設它會設定為`true`，且如果 deleteAfterMinutes 過期，到雲端儲存體上傳時，它會保留 blob。 您可以將它設定為`false`和 deleteAfterMinutes 到期時，它將刪除的資料。 注意：此屬性才能運作 uploadOn 梇糔飶為 true|

## <a name="configure-log-files"></a>設定記錄檔

如需設定您的模組的記錄檔的資訊，請參閱這些[生產環境的最佳作法](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>連接到您的 Blob 儲存體模組

您可以使用帳戶名稱和您為模組設定的帳戶金鑰來存取 IoT Edge 裝置上的 Blob 儲存體。

指定 IoT Edge 裝置作為您提出的任何儲存體要求所用的 Blob 端點。 您可以使用 IoT Edge 裝置資訊和您設定的帳戶名稱，[建立明確儲存體端點的連接字串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

- 針對部署在 Azure Blob 儲存體，IoT Edge 模組上執行的所在相同的裝置的模組，則 blob 端點： `http://<module name>:11002/<account name>`。
- 外部模組或應用程式執行於 Azure Blob 儲存體，在 IoT Edge 模組是執行，則視您的網路設定，其中在不同裝置上，資料流量從您的外部模組或應用程式可以連線到裝置IoT Edge 模組上執行 Azure Blob 儲存體，blob 端點會是其中一個：
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 儲存體快速入門範例

Azure Blob 儲存體文件包含數種語言的快速入門範例程式碼。 您可以執行這些範例以測試變更的 blob 端點，來連接至您的本機 blob 儲存體模組的 IoT Edge 上的 Azure Blob 儲存體。

下列快速入門範例會使用也支援 IoT Edge，因此您無法將它們部署為 IoT Edge 模組，以及 blob 儲存體模組的語言：

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>連接到您的本機儲存體使用 Azure 儲存體總管

您可以使用[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)連接到您的本機儲存體帳戶。

1. 下載並安裝 Azure 儲存體總管

1. 連接到 Azure 儲存體使用的連接字串

1. 提供連接字串： `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 瀏覽至連線的步驟。

1. 建立您的本機儲存體帳戶內的容器

1. 開始上傳為區塊 blob 的檔案。
   > [!NOTE]
   > 此模組不支援分頁 blob。

1. 您可以選擇連接您的 Azure 儲存體帳戶，您要上傳資料。 它可讓您單一檢視您的本機儲存體帳戶與 Azure 儲存體帳戶

## <a name="supported-storage-operations"></a>支援的儲存體作業

IoT Edge 上的 Blob 存储模块使用相同的 Azure 存储 SDK，并与适用于块 Blob 终结点的 2017-04-17 版 Azure 存储 API 保持一致。 日後的版本端視客戶的需求而定。

因為並非所有的 Azure Blob 儲存體作業的 IoT Edge 上的 Azure Blob 儲存體支援，則此區段會列出每個狀態。

### <a name="account"></a>帳戶

支援：

- 列出容器

不支援：

- 取得和設定 Blob 服務屬性
- 預檢 Blob 要求
- 取得 Blob 服務統計資料
- 取得帳戶資訊

### <a name="containers"></a>容器

支援：

- 建立和刪除容器
- 取得容器屬性和中繼資料
- 列出 Blob
- 取得和設定容器 ACL
- 設定容器中繼資料

不支援：

- 租用容器

### <a name="blobs"></a>Blob

支援：

- 放置、取得和刪除 Blob
- 取得和設定 Blob 屬性
- 取得和設定 Blob 中繼資料

不支援：

- 租用 Blob
- 拍攝 Blob 的快照
- 複製和中止複製 Blob
- 取消刪除 Blob
- 設定 Blob 層

### <a name="block-blobs"></a>區塊 Blob

支援：

- 放置區塊
- 放置和取得區塊清單

不支援：

- 從 URL 放置區塊

## <a name="release-notes"></a>版本資訊

以下是[版本資訊中 docker 中樞](https://hub.docker.com/_/microsoft-azure-blob-storage)此模組

## <a name="feedback"></a>意見反應

您的意見反應對我們有用且易於使用，讓此模組和其功能至關重要。 請分享您的意見反應，讓我們知道我們如何改進。

您可以連絡我們 absiotfeedback@microsoft.com

## <a name="next-steps"></a>後續步驟

深入了解[IoT Edge 上部署 Azure Blob 儲存](how-to-deploy-blob.md)
