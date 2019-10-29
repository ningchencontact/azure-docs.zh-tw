---
title: 回應 Blob 儲存體模組事件-Azure 事件方格 IoT Edge |Microsoft Docs
description: 回應 Blob 儲存體模組事件
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 10/02/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d61dc9d49053cb8a125362ac492f354fb64b79a5
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992167"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>教學課程：回應 IoT Edge 上的 Blob 儲存體事件（預覽）

本文說明如何使用事件方格，在 IoT Edge 上以本機方式回應 Blob 建立和 Blob 刪除事件。

常見的 Blob 儲存體事件案例包括映像或影片處理、搜尋索引，或任何檔案導向的工作流程。 非同步檔案上傳非常適合事件。 在變更不常見但情況需要立即回應的情況下，以事件為基礎的架構可能特別有效。

如需 IoT Edge 上 Azure Blob 儲存體的總覽，請參閱[IoT Edge 上的 Azure Blob 儲存體](../../iot-edge/how-to-store-data-blob.md)及其功能。

> [!WARNING]
> 與事件方格 IoT Edge 整合的 Azure Blob 儲存體現供預覽

為了完成本教學課程，您將需要：

* **Azure 訂**用帳戶-如果您還沒有帳戶，請建立一個[免費帳戶](https://azure.microsoft.com/free)。 
* **Azure IoT 中樞和 IoT Edge 裝置**-遵循[Linux](../../iot-edge/quickstart-linux.md)或[Windows 裝置](../../iot-edge/quickstart.md)的快速入門中的步驟（如果您還沒有的話）。

## <a name="deploy-event-grid-iot-edge-module"></a>IoT Edge 模組部署事件方格

有數種方式可以將模組部署到 IoT Edge 裝置，並在 IoT Edge 的 Azure 事件方格中使用它們。 本文說明在 Azure 入口網站的 IoT Edge 上部署事件方格的步驟。

>[!NOTE]
> 在本教學課程中，您將會部署事件方格模組，而不會持續保留。 這表示當您重新部署模組時，將會刪除您在本教學課程中建立的任何主題和訂用帳戶。 如需如何設定持續性的詳細資訊，請參閱下列文章：[在 Linux 中保存狀態](persist-state-linux.md)或[在 Windows 中保存狀態](persist-state-windows.md)。 對於生產工作負載，我們建議您安裝具有持續性的事件方格模組。


### <a name="select-your-iot-edge-device"></a>選取您的 IoT Edge 裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 瀏覽至您的 IoT 中樞。
1. 從 [**自動裝置管理**] 區段的功能表中選取 [ **IoT Edge** ]。 
1. 從裝置清單中按一下目標裝置的識別碼
1. 選取 [設定模組]。 保持開啟頁面。 您將繼續進行下一節中的步驟。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 Azure 入口網站有一個 wizard，會逐步引導您建立部署資訊清單，而不是手動建立 JSON 檔。  它有三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，選取 [**新增**]
1. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
1. 提供容器的 [名稱]、[映射]、[容器] 建立選項：

   * **名稱**： eventgridmodule
   * **映射 URI**： `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器建立選項**：

    ```json
        {
          "Env": [
           "inbound:serverAuth:tlsPolicy=enabled",
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. 按一下 [儲存]
 1. 繼續進行下一節以新增 Azure Functions 模組

    >[!IMPORTANT]
    > 在本教學課程中，您將部署事件方格模組，以允許 HTTP/HTTPs 要求、用戶端驗證已停用，以及允許 HTTP 訂閱者。 對於生產工作負載，建議您只啟用已啟用用戶端驗證的 HTTPs 要求和訂閱者。 如需有關如何安全地設定 Event Grid 模組的詳細資訊，請參閱[安全性和驗證](security-authentication.md)。
    

## <a name="deploy-azure-function-iot-edge-module"></a>部署 Azure Function IoT Edge 模組

本節說明如何部署 Azure Functions IoT 模組，其可做為事件方格訂閱者，以便傳遞事件。

>[!IMPORTANT]
>在本節中，您將部署範例 Azure 函數型訂閱模組。 當然，它可以是任何可接聽 HTTP POST 要求的自訂 IoT 模組。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，再次選取 [**新增**]。 
1. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
1. 提供容器的 [名稱]、[映射] 和 [容器] 建立選項：

   * **名稱**：訂閱者
   * **映射 URI**： `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber-azfunc:latest`
   * **容器建立選項**：

       ```json
            {
              "HostConfig": {
                "PortBindings": {
                  "80/tcp": [
                    {
                      "HostPort": "8080"
                    }
                  ]
                }
              }
            }
       ```

1. 按一下 [儲存]
1. 繼續進行下一節以新增 Azure Blob 儲存體模組

> [!NOTE]
> Blob 儲存體模組會使用 HTTP 發佈事件。 確認「事件方格」模組使用下列設定來允許 HTTP 和 HTTPS 要求： `inbound:serverAuth:tlsPolicy=enabled`。

## <a name="deploy-azure-blob-storage-module"></a>部署 Azure Blob 儲存體模組

本節說明如何部署 Azure Blob 儲存體模組，這會作為事件方格發行者發佈 Blob 已建立和刪除事件。

### <a name="add-modules"></a>新增模組

1. 在 [**部署模組**] 區段中，選取 [**新增**]
2. 從下拉式清單中的模組類型，選取 [ **IoT Edge 模組**]
3. 提供容器的 [名稱]、[映射] 和 [容器] 建立選項：

   * **名稱**： azureblobstorageoniotedge
   * **映射 URI**： mcr.microsoft.com/azure-blob-storage:1.2.2-preview
   * **容器建立選項**：

```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
```

4. 使用下列資訊來更新您複製的 JSON：

   - 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱的長度必須是3到24個字元，且包含小寫字母和數位。 沒有空格。

   - 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

   - 以事件方格模組的名稱取代 `<event grid module name>`。
   - 根據您的容器作業系統來取代 `<storage mount>`。
     - 針對 Linux 容器，**我的磁片區：/blobroot**
     - 對於 Windows 容器，**我的磁片區： C：/blobroot 與**

5. 按一下 [儲存]
6. 按 **[下一步]** 繼續前往 [路由] 區段

 ### <a name="setup-routes"></a>設定路由

保留預設路由，然後選取 **[下一步]** 以繼續前往 [審核] 區段

### <a name="review-deployment"></a>檢閱部署

1. [審查] 區段會顯示根據您在上一節中的選擇所建立的 JSON 部署資訊清單。 確認您看到下列四個模組： [ **$edgeAgent**]、[ **$edgeHub**]、[ **eventgridmodule**]、[**訂閱者**] 和所有已部署的**azureblobstorageoniotedge** 。
2. 檢閱您的部署資訊，然後選取 [提交]。

## <a name="verify-your-deployment"></a>驗證您的部署

1. 提交部署之後，您會返回 IoT 中樞的 [IoT Edge] 頁面。
2. 選取您以部署為目標的**IoT Edge 裝置**，以開啟其詳細資料。
3. 在 [裝置詳細資料] 中，確認 [eventgridmodule]、[訂閱者] 和 [azureblobstorageoniotedge] 模組均列為 [**部署中**] 和 [**由裝置回報**]。

   模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。

## <a name="publish-created-and-deleted-events"></a>發行已建立和刪除的事件

1. 此模組會自動建立主題**MicrosoftStorage**。 確認它存在
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    範例輸出：

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

2. 訂閱者可以註冊發佈至主題的事件。 若要接收任何事件，您必須建立**MicrosoftStorage**主題的 event Grid 訂用帳戶。
    1. 使用下列內容建立 blobsubscription。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > **EndpointType**屬性會指定訂閱者為**Webhook**。  **EndpointUrl**會指定訂閱者接聽事件的 URL。 此 URL 會對應至您稍早部署的 Azure 函式範例。

    2. 執行下列命令來建立主題的訂用帳戶。 確認您看到 [HTTP 狀態碼] 為 [`200 OK`]。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    3. 執行下列命令，確認已成功建立訂用帳戶。 應傳回200正常的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
    ```

    範例輸出：

    ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "http://subscriber:80/api/subscriber"
              }
            }
          }
        }
    ```

2. 下載[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)並[將它連接到您的本機儲存體](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>驗證事件傳遞

### <a name="verify-blobcreated-event-delivery"></a>確認 Microsoft.storage.blobcreated 事件傳遞

1. 將檔案以區塊 blob 的形式上傳至 Azure 儲存體總管的本機儲存體，此模組將會自動發行建立事件。 
2. 查看 [建立事件] 的訂閱者記錄檔。 請遵循下列步驟來[確認事件傳遞](pub-sub-events-webhook-local.md#verify-event-delivery)

    輸出範例：

    ```json
            Received event data [
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

### <a name="verify-blobdeleted-event-delivery"></a>確認 BlobDeleted 事件傳遞

1. 使用 Azure 儲存體總管從本機儲存體中刪除 blob，模組將會自動發行刪除事件。 
2. 查看訂閱者記錄檔以瞭解刪除事件。 請遵循下列步驟來[確認事件傳遞](pub-sub-events-webhook-local.md#verify-event-delivery)

    輸出範例：
    
    ```json
            Received event data [
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
          ]
    ```

恭喜！ 您已完成本教學課程。 下列各節提供事件屬性的詳細資料。

### <a name="event-properties"></a>事件屬性

以下是支援的事件屬性及其類型和描述的清單。 

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 | blob 儲存體帳戶。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 說明 |
| -------- | ---- | ----------- |
| api | 字串 | 觸發事件的作業。 它可以是下列其中一個值： <ul><li>Microsoft.storage.blobcreated-允許的值包括： `PutBlob` 和 `PutBlockList`</li><li>BlobDeleted-允許的值為 `DeleteBlob`、`DeleteAfterUpload` 和 `AutoDelete`。 <p>當自動刪除 blob 時，會產生 `DeleteAfterUpload` 事件，因為 deleteAfterUpload desired 屬性會設定為 true。 </p><p>當 blob 因為 deleteAfterMinutes 所需的屬性值過期而自動刪除時，就會產生 `AutoDelete` 事件。</p></li></ul>|
| clientRequestId | 字串 | 用於儲存體 API 作業的用戶端提供要求識別碼。 此識別碼可用來在記錄檔中使用「用戶端要求識別碼」欄位與 Azure 儲存體診斷記錄相互關聯，並可在使用「x-ms-用戶端要求-識別碼」標頭的用戶端要求中提供。 如需詳細資訊，請參閱[記錄格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| requestId | 字串 | 儲存體 API 作業由服務產生的要求識別碼。 可用於利用記錄中的 "request-id-header" 欄位與 Azure 儲存體診斷記錄建立關聯，並從 'x-ms-request-id' 標頭中的 API 呼叫初始化傳回。 請參閱[記錄格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。 |
| etag | 字串 | 此值可讓您依條件執行作業。 |
| contentType | 字串 | 為 blob 指定內容類型。 |
| contentLength | integer | Blob 大小 (以位元組為單位)。 |
| blobType | 字串 | Blob 的類型。 有效值為 "BlockBlob" 或 "PageBlob"。 |
| url | 字串 | Blob 的路徑。 <br>如果用戶端使用 Blob REST API，則 url 會有下列結構： *\<儲存體帳戶名稱\>. blob.core.windows.net/\<容器名稱*\>/\<檔案名\>。 <br>如果用戶端使用 Data Lake Storage REST API，則 url 會有下列結構： *\<儲存體帳戶名稱\>. dfs.core.windows.net/* \<檔案名\>/\<檔案名。\> |


## <a name="next-steps"></a>後續步驟

請參閱 Blob 儲存體檔中的下列文章：

- [篩選 Blob 儲存體事件](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [使用 Blob 儲存體事件的建議做法](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

在本教學課程中，您會藉由在 Azure Blob 儲存體中建立或刪除 blob 來發佈事件。 請參閱其他教學課程，以瞭解如何將事件轉送至雲端（Azure 事件中樞或 Azure IoT 中樞）： 

- [將事件轉寄至 Azure 事件方格](forward-events-event-grid-cloud.md)
- [將事件轉送至 Azure IoT 中樞](forward-events-iothub.md)