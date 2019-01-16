---
title: 將區塊 Blob 儲存在裝置上 - Azure IoT Edge | Microsoft Docs
description: 將 Azure Blob 儲存體模組部署到您的 IoT Edge 裝置，即可在邊緣儲存資料。
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 01/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b5dcc4295467aee82547bc2a0894320bd110998f
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065812"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>在 IoT Edge (預覽) 使用 Azure Blob 儲存體，以便在邊緣儲存資料

IoT Edge 上的 Azure Blob 儲存體提供邊緣的[區塊 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 儲存體解決方案。 IoT Edge 裝置上的 Blob 儲存體模組如同 Azure 區塊 Blob 服務般運作，但是區塊 Blob 會在本機 IoT Edge 裝置上儲存。 您可以使用平常使用的相同 Azure 儲存體 SDK 方法或區塊 Blob API 呼叫存取您的 Blob。 

資料 (例如影片、影像、財務資料、醫療資料或任何資料) 必須存放在本機並稍後在本機處理或傳輸到雲端的案例，就是使用此課程模組的絕佳範例。

本文提供在對於 IoT Edge 裝置執行 Blob 服務的 IoT Edge 容器上部署 Azure Blob 儲存體的指示。 

>[!NOTE]
>IoT Edge 上的 Azure Blob 儲存體屬於[公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

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

   4. 選取 [ **儲存**]。

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

5. 儲存 **deployment.template.json** 檔案。

6. 開啟方案工作區中的 **.env** 檔案。 

7. .env 檔案是設定成接收登錄認證，但是 Blob 儲存體可公開取得，所以您不需要此設定。 相反地，使用兩個新環境變數來取代檔案： 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. 為 `STORAGE_ACCOUNT_NAME` 提供值，帳戶名稱應該有 3 到 24 個字元，且必須包含小寫字母與數字。 為 `STORAGE_ACCOUNT_KEY` 提供 64 位元組 base64 金鑰。 您可以 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。 

   請勿使用空格引號括住您提供的值。 

9. 儲存 **.env** 檔案。 

10. 以滑鼠右鍵按一下 **deployment.template.json**，然後選取 [產生 IoT Edge 部署資訊清單]。 

11. Visual Studio Code 會採用您在 deployment.template.json 和 .env 中提供的資訊，並使用這些資訊建立新的部署資訊清單檔。 隨即在解決方案工作區新 **config** 資料夾中建立部署資訊清單。 您有該檔案時，就可以依照[從 Visual Studio Code 部署 Azure IoT Edge 模組](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模組](how-to-deploy-modules-cli.md)中的步驟進行。

## <a name="connect-to-your-blob-storage-module"></a>連接到您的 Blob 儲存體模組

您可以使用帳戶名稱和您為模組設定的帳戶金鑰來存取 IoT Edge 裝置上的 Blob 儲存體。 

指定 IoT Edge 裝置作為您提出的任何儲存體要求所用的 Blob 端點。 您可以使用 IoT Edge 裝置資訊和您設定的帳戶名稱，[建立明確儲存體端點的連接字串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。 

1. 針對部署在相同 Edge 裝置 (「IoT Edge 上的 Azure Blob 儲存體」執行所在) 上的模組，Blob 端點是：`http://<module name>:11002/<account name>`。 
2. 針對部署在不同 Edge 裝置 (與「IoT Edge 上的 Azure Blob 儲存體」執行所在 Edge 裝置不同的 Edge 裝置) 上的模組，則取決於您的 Blob 端點設定方式，可能為：`http://<device IP >:11002/<account name>` 或 `http://<IoT Edge device hostname>:11002/<account name>` 或 `http://<FQDN>:11002/<account name>`

## <a name="logs"></a>記錄檔

您可以在容器內的下列位置下找到記錄： 
* 針對 Linux：/blobroot/logs/platformblob.log

## <a name="deploy-multiple-instances"></a>部署多個執行個體

如果您想要部署 IoT Edge 上的多個 Azure Blob 儲存體執行個體，您只需要變更模組所繫結的 HostPort。 Blob 儲存體模組一律會公開容器的 11002 連接埠，但是您可以宣告哪個連接埠繫結至主機。 

編輯模組建立選項，以變更 HostPort 值：

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

您連接到其他 Blob 儲存體模組時，請變更端點來指向已更新的主機連接埠。 

### <a name="try-it-out"></a>試做

Azure Blob 儲存體文件集包括有數種語言的範例程式碼的快速入門。 您可以執行這些範例變更 Blob 端點指向 Blob 儲存體模組，以便在 IoT Edge 上測試 Azure Blob 儲存體。

下列快速入門使用 IoT Edge 也支援的語言，因此您可以連同 Blob 儲存體模組將這些語言部署為 IoT Edge 模組：

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>支援的儲存體作業

IoT Edge 上的 Blob 儲存體模組使用相同的 Azure 儲存體 SDK，而且與區塊 Blob 端點的 Azure 儲存體 API 2018-03-28 版本一致。 日後的版本端視客戶的需求而定。 

IoT Edge 上的 Azure Blob 儲存體並未完全支援所有的 Azure Blob 儲存體作業。 下列各節詳細說明如何不支援的作業。 

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

不支援： 
* 取得和設定容器 ACL
* 租用容器
* 設定容器中繼資料

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
* 放置區塊：區塊大小必須小於或等於 4 MB
* 放置和取得區塊清單

不支援：
* 從 URL 放置區塊

## <a name="next-steps"></a>後續步驟

深入了解 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)

