---
title: 將區塊 Blob 儲存在裝置上 - Azure IoT Edge | Microsoft Docs
description: 將 Azure Blob 儲存體模組部署到您的 IoT Edge 裝置，即可在邊緣儲存資料。
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0fc34c913453abd174009213233a54e30b9346d3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881379"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>在 IoT Edge (預覽) 使用 Azure Blob 儲存體，以便在邊緣儲存資料

IoT Edge 上的 Azure Blob 儲存體提供邊緣的[區塊 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 儲存體解決方案。 IoT Edge 裝置上的 Blob 儲存體模組如同 Azure 區塊 Blob 服務般運作，但是區塊 Blob 會在本機 IoT Edge 裝置上儲存。 您可以使用平常使用的相同 Azure 儲存體 SDK 方法或區塊 Blob API 呼叫存取您的 Blob。 

此模块附带**自动分层**和**自动过期**功能。

> [!NOTE]
> 目前，自动分层和自动过期功能仅在 Linux AMD64 和 Linux ARM32 中可用。

**自动分层**是可配置的功能，可用于将本地 Blob 存储中的数据自动上传到 Azure，并提供间歇性的 Internet 连接支持。 该功能允许：
- 启用/禁用分层功能
- 选择将数据复制到 Azure 的顺序，例如，选择 NewestFirst 或 OldestFirst
- 指定要将数据上传到的 Azure 存储帐户。
- 指定要上传到 Azure 的容器。 此模块允许指定源和目标容器名称。
- 执行完整 Blob 分层（使用 `Put Blob` 操作）和块级分层（使用 `Put Block` 和 `Put Block List` 操作）。

如果 Blob 由块构成，则此模块使用块级分层。 下面是一些常见场景：
- 应用程序需要更新以前上传的 Blob 的某些块；此模块只上传更新的块，而不会上传整个 Blob。
- 当模块正在上传 Blob 时，Internet 连接断开；连接恢复后，该模块只上传剩余的块，而不会上传整个 Blob。

如果在 Blob 上传期间发生意外的进程终止（例如电源故障），当模块重新联机时，将再次上传需要上传的所有块。

**自动过期**是可配置的功能，当本地存储中的 Blob 达到生存时间 (TTL) 时，此模块会使用此功能自动删除这些 Blob。 生存时间以分钟为单位。 该功能允许：
- 启用/禁用自动过期功能
- 以分钟为单位指定 TTL

对于需要在本地存储视频、图像、金融数据、医院数据或其他任何数据，以后在本地处理数据或将其传输到云的场合，都很适合使用此模块。

本文提供在對於 IoT Edge 裝置執行 Blob 服務的 IoT Edge 容器上部署 Azure Blob 儲存體的指示。 

>[!NOTE]
>IoT Edge 上的 Azure Blob 儲存體屬於[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

觀看快速簡介影片
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>必要條件

Azure IoT Edge 裝置：

* 您可以遵循 [Linux](quickstart-linux.md) 或 [Windows 裝置](quickstart.md)快速入門中的步驟，使用您的開發電腦或虛擬機器作為邊緣裝置。
* IoT Edge 模組上的 Azure Blob 儲存體支援下列裝置設定：

   | 作業系統 | 架構 |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (10 月更新) | AMD64 |
   | Windows 10 IoT Enterprise (10 月更新) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

雲端資源：

* Azure 中的標準層 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。 


## <a name="deploy-blob-storage-to-your-device"></a>將 Blob 儲存體部署至您的裝置

有數種方式可將模組部署到 IoT Edge 裝置，而且這些方式皆適用於 IoT Edge 模組上的 Azure Blob 儲存體。 兩個最簡單的方法是使用 Azure 入口網站或 Visual Studio Code 範本。 

### <a name="azure-portal"></a>Azure 入口網站

Azure Marketplace 提供可以直接部署至您 IoT Edge 裝置 (包括 IoT Edge 上的 Azure Blob 儲存體) 的 IoT Edge 模組。 請遵循下列步驟來從 Azure 入口網站部署模組。

1. 在 [Azure 入口網站](https://portal.azure.com)中，搜尋「Azure Blob Storage on IoT Edge」。 並從 Marketplace 搜尋結果中**選取**。

   ![從 Marketplace 搜尋建立模組](./media/how-to-store-data-blob/marketplace-module.png)

2. 選擇用來接收此模組的 IoT Edge 裝置。 在 [IoT Edge 模組的目標裝置] 頁面上，提供下列資訊：

   1. 選取您要使用的 IoT 中樞所屬的**訂用帳戶**。

   2. 選取您的 **IoT 中樞**。

   3. 如果您知道您 **IoT Edge 裝置的名稱**，請在文字方塊中名稱。 或者選取 [尋找裝置]，以從 IoT 中樞的 IoT Edge 裝置清單中選擇裝置。 
   
   4. 選取 [建立] 。

   您現在已從 Azure Marketplace 中選擇 IoT Edge 模組，並選擇用來接收模組的 IoT Edge 裝置，接著您將前往可協助您明確定義如何部署模組的三步驟精靈。

3. 在 [設定模組精靈] 的 [新增模組] 步驟中，留意到 [AzureBlobStorageonIoTEdge] 模組已列在 [部署模組] 下。 

2. 從部署模組清單中選取 Blob 儲存體模組，以開啟模組詳細資料。 

   ![選取模組名稱以開啟模組詳細資料](./media/how-to-store-data-blob/open-module-details.png)

3. 在 [IoT Edge 自訂模組] 頁面上，使用下列步驟來更新 Azure Blob 儲存體，在 IoT Edge 模組：

   1. 將模組**名稱**變更為小寫。 您可以將模組重新命名，或使用 `azureblobstorageoniotedge`。 

      >[!IMPORTANT]
      >當您呼叫模組時，Azure IoT Edge 會區分大小寫，且儲存體 SDK 預設為小寫。 若要確保您 IoT Edge 模組上的 Azure Blob 儲存體連線不中斷，請為它提供小寫名稱。 

   2. 您的容器需要預設的 [容器建立選項] 連接埠繫結，但您也需要新增您的儲存體帳戶資訊，而且也需要裝置上儲存體目錄的繫結。 使用下面的 JSON 覆寫入口網站中的 JSON：
    
      ```json
      {
          "Env":[
              "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
              "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
          ],
          "HostConfig":{
              "Binds":[
                  "<storage directory bind>"
              ],
              "PortBindings":{
                  "11002/tcp":[{"HostPort":"11002"}]
              }
          }
      }
      ```   
   3. 使用下列資訊來更新您複製的 JSON： 

      * 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱應該有 3 到 24 個字元，包含小寫字母和數字。
      * 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。
      * 視您的容器作業系統而定來取代 `<storage directory bind>`。 提供[磁碟區](https://docs.docker.com/storage/volumes/)的名稱，或您想要 Blob 模組儲存其資料的 IoT Edge 裝置目錄絕對路徑。 儲存體目錄繫結會對應模組中您提供要設定位置的裝置。 

         * Linux 容器：[\<儲存體路徑>:/blobroot]。 例如，/srv/containerdata:/blobroot。 或者，my-volume:/blobroot。 
         * Windows 容器：[\<儲存體路徑>:C:/BlobRoot]。 例如，C:/ContainerData:C:/BlobRoot。 或者，my-volume:C:/blobroot。
   
      > [!IMPORTANT]
      > 請勿變更儲存體目錄繫結的後半段 (指向模組中的特定位置)。 此儲存體目錄繫結的結尾對於 Linux 容器應一律為 **:/blobroot**，對於 Windows 容器應一律為 **:C:/BlobRoot**。

      ![更新模組容器建立選項 - 入口網站](./media/how-to-store-data-blob/edit-module.png)

   4. 在所需属性中设置[自动分层和自动过期](#configure-auto-tiering-and-auto-expiration-via-azure-portal)。 [自动分层](#auto-tiering-properties)和[自动过期](#auto-expiration-properties)属性及其可能值的列表。 

   5. 選取 [ **儲存**]。 

4. 選取 [下一步]，繼續進行精靈的下一個步驟。
5. 在精靈的 [指定路由] 步驟中，選取 [下一步]。
6. 在精靈的 [檢閱部署] 步驟中，選取 [提交]。
7. 提交部署之後，您會返回您 IoT 中樞的 [IoT Edge] 頁面。 選取您使用部署設定為目標的 IoT Edge 裝置，以開啟其詳細資料。 
8. 在裝置詳細資料中，確認 Blob 儲存體模組列為 [指定於部署中] 和 [由裝置回報]。 模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。 

### <a name="visual-studio-code-templates"></a>Visual Studio Code 範本

Azure IoT Edge 提供 Visual Studio Code 中的範本協助您開發解決方案。 這些步驟需要您在您的開發機器上安裝 [Visual Studio Code](https://code.visualstudio.com/)，並設定 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) \(英文\)。

下列步驟可用來使用 Blob 儲存體模組建立新的 IoT Edge 解決方案，並設定部署資訊清單。 

1. 選取 [檢視] > [命令選擇區]。 

2. 在命令選擇區中，輸入並執行命令 Azure IoT Edge：新增 IoT Edge 解決方案。 依照命令選擇區中的提示建立解決方案。

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇開發機器上可供 VS Code 建立解決方案檔案的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱或接受預設值 **EdgeSolution**。 |
   | 選取模組範本 | 選擇 [現有模組 (輸入完整映像 URL)]。 |
   | 提供模組名稱 | 為您的模組輸入全部小寫名稱，例如 **azureblobstorage**。<br><br>針對 IoT Edge 模組上的 Azure Blob 儲存體，請務必使用小寫名稱。 IoT Edge 在參考模組時會區分大小寫，且儲存體 SDK 預設為小寫。 |
   | 提供模組的 Docker 映像 | 提供映像 URI：**mcr.microsoft.com/azure-blob-storage:latest** |

   VS Code 會採用您提供的資訊、建立 IoT Edge 解決方案，然後將其載入至新的視窗。 解決方案範本會建立部署資訊清單範本，其中包含 Blob 儲存體模組映像，但是您需要設定模組的建立選項。 

3. 在新的解決方案工作區中開啟 [deployment.template.json]，並找出 [模組] 區段。 進行下列設定變更：

   1. 刪除這次部署不需要的 **tempSensor** 模組。 

   2. 複製下列程式碼，並在 Blob 儲存體模組的 [createOptions] 欄位貼上： 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![更新模組 createOptions - VS Code](./media/how-to-store-data-blob/create-options.png)

4. 在建立選項 JSON 中，按照您的容器作業系統更新 `<storage directory bind>`。 提供[磁碟區](https://docs.docker.com/storage/volumes/)的名稱，或您想要 Blob 模組儲存其資料的 IoT Edge 裝置目錄絕對路徑。 儲存體目錄繫結會對應模組中您提供要設定位置的裝置。  

   * Linux 容器：[\<儲存體路徑>:/blobroot]。 例如，/srv/containerdata:/blobroot。 或者，my-volume:/blobroot。
   * Windows 容器：[\<儲存體路徑>:C:/BlobRoot]。 例如，C:/ContainerData:C:/BlobRoot。 或者，my-volume:C:/blobroot。
   
   > [!IMPORTANT]
   > 請勿變更儲存體目錄繫結的後半段 (指向模組中的特定位置)。 儲存體目錄繫結的結尾對於 Linux 容器應一律為 **:/blobroot**，對於 Windows 容器應一律為 **:C:/BlobRoot**。

5. 配置[自动分层和自动过期](#configure-auto-tiering-and-auto-expiration-via-vscode)。 [自动分层](#auto-tiering-properties)和[自动过期](#auto-expiration-properties)属性的列表

6. 儲存 **deployment.template.json** 檔案。

7. 開啟方案工作區中的 **.env** 檔案。 

8. .env 檔案是設定成接收登錄認證，但是 Blob 儲存體可公開取得，所以您不需要此設定。 相反地，使用兩個新環境變數來取代檔案： 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. 為 `STORAGE_ACCOUNT_NAME` 提供值，帳戶名稱應該有 3 到 24 個字元，且必須包含小寫字母與數字。 為 `STORAGE_ACCOUNT_KEY` 提供 64 位元組 base64 金鑰。 您可以 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。 

   請勿使用空格引號括住您提供的值。 

10. 儲存 **.env** 檔案。 

11. 以滑鼠右鍵按一下 **deployment.template.json**，然後選取 [產生 IoT Edge 部署資訊清單]。 

12. Visual Studio Code 會採用您在 deployment.template.json 和 .env 中提供的資訊，並使用這些資訊建立新的部署資訊清單檔。 隨即在解決方案工作區新 **config** 資料夾中建立部署資訊清單。 您有該檔案時，就可以依照[從 Visual Studio Code 部署 Azure IoT Edge 模組](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模組](how-to-deploy-modules-cli.md)中的步驟進行。

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>自动分层和自动过期属性与配置

使用所需属性设置自动分层和自动过期属性。 可以在部署期间设置这些属性，或者，以后可以通过编辑模块孪生来更改这些属性，而无需重新部署。 我们建议检查“模块孪生”中的 `reported configuration` 和 `configurationValidation`，以确保正确传播值。

### <a name="auto-tiering-properties"></a>自动分层属性 
此设置的名称为 `tieringSettings`

| 欄位 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| tieringOn | true、false | 默认设置为 `false`，若要启用它，可将它设置为 `true`|
| backlogPolicy | NewestFirst、OldestFirst | 用于选择将数据复制到 Azure 的顺序。 默认设置为 `OldestFirst`。 顺序由 Blob 的上次修改时间确定 |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 是一个连接字符串，用于指定要将数据上传到的 Azure 存储帐户。 指定 `Azure Storage Account Name`、`Azure Storage Account Key` 或 `End point suffix`。 新增適當 EndpointSuffix 的 Azure 位置將上傳資料，而不同的全域 Azure Government Azure，Microsoft Azure Stack。 |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 用于指定要上传到 Azure 的容器名称。 此模块允许指定源和目标容器名称。 如果未指定目标容器名称，系统会自动分配 `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>` 作为容器名称。 可以创建目标容器名称的模板字符串，具体请查看“可能的值”列。 <br>* %h -> IoT 中心名称（3 到 50 个字符）。 <br>* %d -> IoT 设备 ID（1 到 129 个字符）。 <br>* %m -> 模块名称（1 到 64 个字符）。 <br>* %c -> 源容器名称（3 到 63 个字符）。 <br><br>容器名称的最大大小为 63 个字符。尽管系统会自动分配目标容器名称，但如果容器大小超过 63 个字符，系统会将每个部分（IoTHubName、IotEdgeDeviceName、ModuleName、ContainerName）修剪为 15 个字符。 |

### <a name="auto-expiration-properties"></a>自动过期属性
此设置的名称为 `ttlSettings`

| 欄位 | 可能的值 | 說明 |
| ----- | ----- | ---- |
| ttlOn | true、false | 默认设置为 `false`，若要启用它，可将它设置为 `true`|
| timeToLiveInMinutes | `<minutes>` | 以分钟为单位指定 TTL。 当本地存储中的 Blob 达到 TTL 时，模块会自动删除这些 Blob |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>通过 Azure 门户配置自动分层和自动过期

若要通过设置所需属性来启用自动分层和自动过期，可以设置以下值：

- **在初始部署期间**：复制“设置模块孪生的所需属性”框中的 JSON。 为每个属性配置适当的值，保存属性，然后继续部署。

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

  ![设置自动分层和自动过期属性](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **通过“模块标识孪生”功能部署模块后**：转到此模块的“模块标识孪生”，复制所需属性下的 JSON，为每个属性配置适当的值，然后保存属性。 在“模块标识孪生”JSON 中，确保每次添加或更新任何所需属性时，`reported configuration` 部分都会反映所做的更改，并且 `configurationValidation` 部分针对每个属性显示成功消息。

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```

![tiering+ttl module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>通过 VSCode 配置自动分层和自动过期

- **在初始部署期间**：在 deployment.template.json 中添加以下 JSON，以定义此模块的所需属性。 为每个属性配置适当的值并保存。

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

下面是此模块的所需属性示例：![设置 azureblobstorageoniotedge 的所需属性 - VS Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **通过“模块孪生”部署模块后**：[编辑此模块的模块孪生](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)，复制所需属性下的 JSON，为每个属性配置适当的值，然后保存属性。 在“模块孪生”JSON 中，确保每次添加或更新任何所需属性时，`reported configuration` 部分都会反映所做的更改，并且 `configurationValidation` 部分针对每个属性显示成功消息。

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```
  ## <a name="logs"></a>記錄檔

請遵循指示以[設定您的 IoT Edge 模組的 docker 記錄](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>連接到您的 Blob 儲存體模組

您可以使用帳戶名稱和您為模組設定的帳戶金鑰來存取 IoT Edge 裝置上的 Blob 儲存體。 

指定 IoT Edge 裝置作為您提出的任何儲存體要求所用的 Blob 端點。 您可以使用 IoT Edge 裝置資訊和您設定的帳戶名稱，[建立明確儲存體端點的連接字串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。 

1. 对于部署在运行“IoT Edge 上的 Azure Blob 存储”的同一边缘设备上的模块，Blob 终结点为：`http://<module name>:11002/<account name>`。 
2. 对于部署在不同边缘设备，而非运行“IoT Edge 上的 Azure Blob 存储”的边缘设备上的模块，Blob 终结点为：`http://<device IP >:11002/<account name>`、`http://<IoT Edge device hostname>:11002/<account name>` 或 `http://<FQDN>:11002/<account name>`，具体取决于设置

## <a name="deploy-multiple-instances"></a>部署多個執行個體

若要在 IoT Edge 上部署 Azure Blob 存储的多个实例，需要提供不同的存储路径，并更改模块绑定到的 HostPort。 Blob 儲存體模組一律會公開容器的 11002 連接埠，但是您可以宣告哪個連接埠繫結至主機。 

編輯模組建立選項，以變更 HostPort 值：

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

您連接到其他 Blob 儲存體模組時，請變更端點來指向已更新的主機連接埠。 

## <a name="try-it-out"></a>試做

### <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 存储快速入门示例
Azure Blob 儲存體文件集包括有數種語言的範例程式碼的快速入門。 您可以執行這些範例變更 Blob 端點指向 Blob 儲存體模組，以便在 IoT Edge 上測試 Azure Blob 儲存體。 遵循相应的步骤[连接到 Blob 存储模块](#connect-to-your-blob-storage-module)

下列快速入門使用 IoT Edge 也支援的語言，因此您可以連同 Blob 儲存體模組將這些語言部署為 IoT Edge 模組：

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Azure 儲存體總管
您也可以嘗試[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)連接到您的本機儲存體帳戶。 我們試著使用[舊版 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0)的 Azure 檔案總管。
> [!NOTE]
> 您可能會執行下列步驟時遇到錯誤，略過並重新整理。 

1. 下載並安裝 Azure 儲存體總管
2. 連接到 Azure 儲存體使用的連接字串
3. 提供連接字串： `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. 瀏覽至連線的步驟。
5. 建立您的本機儲存體帳戶內的容器
6. 開始上傳為區塊 blob 的檔案。
   > [!NOTE]
   > 取消選取核取方塊，以將它上傳做為分頁 blob。 此模組不支援分頁 blob。 您會看到此提示在上傳檔案，如.iso、.vhd、.vhdx 或任何大型檔案時。

7. 您可以選擇連接您的 Azure 儲存體帳戶，您要上傳資料。 它可讓您單一檢視您的本機儲存體帳戶與 Azure 儲存體帳戶

## <a name="supported-storage-operations"></a>支援的儲存體作業

IoT Edge 上的 Blob 存储模块使用相同的 Azure 存储 SDK，并与适用于块 Blob 终结点的 2017-04-17 版 Azure 存储 API 保持一致。 日後的版本端視客戶的需求而定。

IoT Edge 上的 Azure Blob 儲存體並未完全支援所有的 Azure Blob 儲存體作業。 以下部分列出了支持和不支持的操作。

### <a name="account"></a>帳戶

支援： 
* 列出容器

不支援： 
* 取得和設定 Blob 服務屬性
* 預檢 Blob 要求
* 取得 Blob 服務統計資料
* 取得帳戶資訊

### <a name="containers"></a>容器

支援： 
* 建立和刪除容器
* 取得容器屬性和中繼資料
* 列出 Blob
* 取得和設定容器 ACL
* 設定容器中繼資料

不支援：
* 租用容器

### <a name="blobs"></a>Blob

支援： 
* 放置、取得和刪除 Blob
* 取得和設定 Blob 屬性
* 取得和設定 Blob 中繼資料

不支援： 
* 租用 Blob
* 拍攝 Blob 的快照
* 複製和中止複製 Blob
* 取消刪除 Blob
* 設定 Blob 層

### <a name="block-blobs"></a>區塊 Blob

支援： 
* 放置區塊
* 放置和取得區塊清單

不支援：
* 從 URL 放置區塊

## <a name="feedback"></a>意見反應:
您的意見反應是對我們有用且易於使用，讓此模組和其功能非常重要。 請分享您的意見反應，讓我們知道我們如何改進。

您可以連絡我們 absiotfeedback@microsoft.com 

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)

