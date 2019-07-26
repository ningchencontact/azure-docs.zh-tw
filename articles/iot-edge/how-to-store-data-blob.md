---
title: 將區塊 Blob 儲存在裝置上 - Azure IoT Edge | Microsoft Docs
description: 瞭解分層和存留時間功能, 請參閱支援的 blob 儲存體作業, 並聯機至您的 blob 儲存體帳戶。
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c5a27a8016202f7f8c9e256eaf6b3077fbef295b
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414526"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>在 IoT Edge (預覽) 使用 Azure Blob 儲存體，以便在邊緣儲存資料

IoT Edge 上的 Azure Blob 儲存體提供邊緣的[區塊 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 儲存體解決方案。 IoT Edge 裝置上的 blob 儲存體模組的運作方式類似 Azure 區塊 blob 服務, 但區塊 blob 會儲存在您的 IoT Edge 裝置本機上。 您可以使用平常使用的相同 Azure 儲存體 SDK 方法或區塊 Blob API 呼叫存取您的 Blob。 本文說明在 IoT Edge 裝置上執行 Blob 服務 IoT Edge 容器 Azure Blob 儲存體相關概念。

此模組適用于資料必須儲存在本機, 直到可以處理或傳輸到雲端為止的情況。 此資料可能是影片、影像、財務資料、醫院資料或任何其他非結構化資料。

> [!NOTE]
> IoT Edge 上的 Azure Blob 儲存體屬於[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

觀賞快速簡介影片
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

此課程模組隨附**deviceToCloudUpload**和**deviceAutoDelete**功能。

**deviceToCloudUpload**是可設定的功能。 此函式會自動將資料從您的本機 blob 儲存體上傳至 Azure, 並提供間歇性的網際網路連線支援。 它可讓您:

- 開啟/關閉 deviceToCloudUpload 功能。
- 選擇資料複製到 Azure 的順序, 例如 NewestFirst 或 OldestFirst。
- 指定您要上傳資料的 Azure 儲存體帳戶。
- 指定您想要上傳至 Azure 的容器。 此模組可讓您指定來源和目標容器名稱。
- 選擇在上傳至雲端儲存體完成後, 立即刪除 blob 的功能
- 執行完整 blob 上傳 ( `Put Blob`使用操作) 和區塊層級上`Put Block`傳`Put Block List` (使用和作業)。

當您的 blob 包含區塊時, 此模組會使用區塊層級上傳。 以下是一些常見案例:

- 您的應用程式會更新先前上傳之 blob 的某些區塊, 此模組只會上傳更新的區塊, 而不是整個 blob。
- 模組正在上傳 blob, 而網際網路連線已中斷, 當連線重新時, 它只會上傳其餘區塊, 而不是整個 blob。

如果在 blob 上傳期間發生非預期的進程終止 (例如電源中斷), 則在模組重新上線之後, 將會再次上傳所有已到期的區塊。

**deviceAutoDelete**是可設定的功能。 此函式會在指定的持續時間 (以分鐘為單位) 到期時, 自動從本機儲存體中刪除您的 blob。 它可讓您:

- 開啟/關閉 deviceAutoDelete 功能。
- 指定將自動刪除 blob 的時間 (以分鐘為單位) (deleteAfterMinutes)。
- 如果 deleteAfterMinutes 值過期, 請選擇在其上傳時保留 blob 的功能。


## <a name="prerequisites"></a>先決條件

Azure IoT Edge 裝置：

- 您可以遵循[Linux](quickstart-linux.md)或[Windows 裝置](quickstart.md)快速入門中的步驟, 使用您的開發電腦或虛擬機器作為 IoT Edge 裝置。

- IoT Edge 模組上的 Azure Blob 儲存體支援下列裝置設定：

  | 作業系統 | AMD64 | ARM32v7 | ARM64 |
  | ---------------- | ----- | ----- | ---- |
  | Raspbian-stretch | 否 | 是 | 否 |  
  | Ubuntu Server 16.04 | 是 | 否 | 是 |
  | Ubuntu Server 18.04 | 是 | 否 | 是 |
  | Windows 10 IoT 企業版, 組建17763 | 是 | 否 | 否 |
  | Windows Server 2019, 組建17763 | 是 | 否 | 否 |
  

雲端資源：

Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 和 deviceAutoDelete 屬性

使用模組所需的屬性來設定**deviceToCloudUploadProperties**和**deviceAutoDeleteProperties**。 您可以在部署期間設定所需的屬性, 或在稍後藉由編輯模組對應項而不需要重新部署來進行變更。 我們建議您檢查和`reported configuration` `configurationValidation`的「模組對應項」, 以確保值已正確傳播。

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

此設定的名稱是`deviceToCloudUploadProperties`

| 欄位 | 可能的值 | 說明 | 環境變數 |
| ----- | ----- | ---- | ---- |
| uploadOn | true、false | 預設設定`false`為。 如果您想要開啟此功能, 請將此欄位設定`true`為。 | `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst、OldestFirst | 可讓您選擇將資料複製到 Azure 的順序。 預設設定`OldestFirst`為。 順序是由上次修改的 Blob 時間所決定 | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`這是一個連接字串, 可讓您指定要上傳資料的 Azure 儲存體帳戶。 指定`Azure Storage Account Name`、 `Azure Storage Account Key`、 。`End point suffix` 新增適當的 Azure EndpointSuffix, 以將資料上傳到其中, 全域 Azure、政府 Azure 和 Microsoft Azure Stack 會有所不同。 | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 可讓您指定您想要上傳至 Azure 的容器名稱。 此模組可讓您指定來源和目標容器名稱。 如果您未指定目標容器名稱, 它會自動將容器名稱指派為`<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`。 您可以建立目標容器名稱的範本字串, 請查看可能的值資料行。 <br>*% h-> IoT 中樞名稱 (3-50 個字元)。 <br>*% d-> IoT Edge 裝置識別碼 (1 到129個字元)。 <br>*% m-> 模組名稱 (1 到64個字元)。 <br>*% c-> 來源容器名稱 (3 到63個字元)。 <br><br>容器名稱的大小上限為63個字元, 而如果容器的大小超過63個字元, 則會自動指派目標容器名稱, 它會將每個區段 (IoTHubName、IotEdgeDeviceID、ModuleName、SourceContainerName) 修剪為15個。長度. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true、false | 預設設定`false`為。 當其設定為`true`時, 會在上傳至雲端儲存體完成時自動刪除資料 | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

此設定的名稱是`deviceAutoDeleteProperties`

| 欄位 | 可能的值 | 說明 | 環境變數 |
| ----- | ----- | ---- | ---- |
| deleteOn | true、false | 預設設定`false`為。 如果您想要開啟此功能, 請將此欄位設定`true`為。 | `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 指定以分鐘為單位的時間。 此模組會在此值到期時, 自動從本機儲存體中刪除您的 blob | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true、false | 根據預設, 它會設定`true`為, 而且如果 deleteAfterMinutes 過期, 它會在將 blob 上傳至雲端儲存體時保留。 您可以將它設定`false`為, 它會在 deleteAfterMinutes 到期時立即刪除資料。 注意:若要讓此屬性工作 uploadOn, 應設定為 true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>設定記錄檔

如需為模組設定記錄檔的詳細資訊, 請參閱這些[生產環境最佳做法](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>連接到您的 Blob 儲存體模組

您可以使用帳戶名稱和您為模組設定的帳戶金鑰來存取 IoT Edge 裝置上的 Blob 儲存體。

指定 IoT Edge 裝置作為您提出的任何儲存體要求所用的 Blob 端點。 您可以使用 IoT Edge 裝置資訊和您設定的帳戶名稱，[建立明確儲存體端點的連接字串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

- 對於部署在 IoT Edge 模組上之 Azure Blob 儲存體所在的相同裝置上的模組, Blob 端點為: `http://<module name>:11002/<account name>`。
- 對於在不同裝置上執行的模組或應用程式, 您必須為您的網路選擇正確的端點。 根據您的網路設定, 選擇端點格式, 讓來自外部模組或應用程式的資料流量可以連接到在 IoT Edge 模組上執行 Azure Blob 儲存體的裝置。 此案例的 blob 端點為下列其中一項:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 儲存體快速入門範例

Azure Blob 儲存體檔包含幾種語言的快速入門範例程式碼。 您可以藉由變更 Blob 端點以連接到本機 Blob 儲存體模組, 來執行這些範例以測試 IoT Edge 上的 Azure Blob 儲存體。

下列快速入門範例會使用 IoT Edge 也支援的語言, 因此您可以將它們部署為與 blob 儲存體模組相同的 IoT Edge 模組:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>使用 Azure 儲存體總管連接到您的本機儲存體

您可以使用[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)來連接到本機儲存體帳戶。

1. 下載並安裝 Azure 儲存體總管

1. 使用連接字串連接到 Azure 儲存體

1. 提供連接字串:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 完成連線的步驟。

1. 在您的本機儲存體帳戶內建立容器

1. 開始將檔案上傳為區塊 blob。
   > [!NOTE]
   > 此模組不支援分頁 blob。

1. 您也可以選擇在儲存體總管中連接您的 Azure 儲存體帳戶。 此設定可為您提供本機儲存體帳戶和 Azure 儲存體帳戶的單一視圖

## <a name="supported-storage-operations"></a>支援的儲存體作業

IoT Edge 上的 Blob 儲存體模組會使用 Azure 儲存體 Sdk, 且與適用于區塊 blob 端點的2017-04-17 版 Azure 儲存體 API 一致。 

因為 IoT Edge 上的 Azure Blob 儲存體不支援所有的 Azure Blob 儲存體作業, 所以此區段會列出每一個的狀態。

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

以下是此課程模組的[docker hub 版本](https://hub.docker.com/_/microsoft-azure-blob-storage)資訊

## <a name="feedback"></a>意見

您的意見反應對我們來說非常重要, 因為它的功能很實用且容易使用。 請分享您的意見反應, 並讓我們知道如何改進。

您可以在這裡找到absiotfeedback@microsoft.com

## <a name="next-steps"></a>後續步驟

深入瞭解如何[在 IoT Edge 上部署 Azure Blob 儲存體](how-to-deploy-blob.md)
