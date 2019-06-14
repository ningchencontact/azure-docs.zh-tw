---
title: 將 Azure Blob 儲存體模組部署到裝置-Azure IoT Edge |Microsoft Docs
description: 將 Azure Blob 儲存體模組部署到您的 IoT Edge 裝置，即可在邊緣儲存資料。
author: kgremban
ms.author: kgremban
ms.date: 05/21/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: philmea
ms.openlocfilehash: d844e81de9cfb556e91ab5c0d5a8074c822cce0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65990475"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>將 Azure Blob 儲存體，在 IoT Edge 模組部署到您的裝置

有數種方式可將模組部署到 IoT Edge 裝置和所有人都適用於 Azure Blob 儲存體，在 IoT Edge 模組。 兩個最簡單的方法是使用 Azure 入口網站或 Visual Studio Code 範本。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
- 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device-portal.md)。
- [Visual Studio Code](https://code.visualstudio.com/)而[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)如果從 Visual Studio Code 部署。

## <a name="deploy-from-the-azure-portal"></a>從 Azure 入口網站部署

Azure 入口網站會引導您建立部署資訊清單，並將部署推送至 IoT Edge 裝置。

### <a name="select-your-device"></a>選取您的裝置

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 選取功能表中的 [IoT Edge]  。
1. 按一下裝置清單中目標裝置的識別碼。
1. 選取 [設定模組]  。

### <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 Azure 入口網站有一個精靈，引導您建立部署資訊清單，而不要手動建立 JSON 文件。 其中包含三個步驟：**新增模組**、**指定路由**和**檢閱部署**。

#### <a name="add-modules"></a>新增模組

1. 在頁面的 [部署模組]  區段中，選取 [新增]  。

1. 從下拉式清單中的模組類型，選取**IoT Edge 模組**。

1. 提供模組的名稱，然後指定 容器映像：

   - **Name** - azureblobstorageoniotedge
   - **映像 URI** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge 是區分大小寫，當您呼叫模組，並 「 儲存體 SDK 也會預設為小寫。 雖然在模組的名稱[Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)是**AzureBlobStorageonIoTEdge**，變更為小寫可協助您確保您 IoT Edge 模組上的 Azure Blob 儲存體的連線名稱不中斷。

1. 預設值**容器建立選項**值會定義您的容器需要連接埠繫結，但您也需要在您的裝置新增儲存體帳戶資訊和儲存體目錄的繫結。 在入口網站中的 JSON 預設值取代為以下的 JSON:

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

1. 使用下列資訊來更新您複製的 JSON：

   - 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱應該是 3 到 24 個字元，包含小寫字母和數字。 不能是空格。

   - 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

   - 取代`<storage directory bind>`根據您容器的作業系統。 提供[磁碟區](https://docs.docker.com/storage/volumes/)的名稱，或您想要 Blob 模組儲存其資料的 IoT Edge 裝置目錄絕對路徑。 儲存體目錄繫結會對應模組中您提供要設定位置的裝置。

     - 適用於 Linux 容器的格式是 *\<儲存體路徑 >: / blobroot*。 例如， **/srv/containerdata: / blobroot**或是**my 磁碟區: / blobroot**。
     - 格式是適用於 Windows 容器 *\<儲存體路徑 >: C: / BlobRoot*。 例如， **c: / ContainerData:C: / BlobRoot**或是**我-磁碟區： C: / blobroot**。

     > [!IMPORTANT]
     > 請勿變更儲存體目錄繫結的後半段 (指向模組中的特定位置)。 此儲存體目錄繫結的結尾對於 Linux 容器應一律為 **:/blobroot**，對於 Windows 容器應一律為 **:C:/BlobRoot**。

    ![更新模組容器建立選項 - 入口網站](./media/how-to-store-data-blob/edit-module.png)

1. 設定[分層](how-to-store-data-blob.md#tiering-properties)並[存留時間](how-to-store-data-blob.md#time-to-live-properties)複製下列 JSON 並貼上到您模組屬性**組模組對應項的所需屬性** 方塊中。 使用適當的值設定每個屬性，並儲存它，繼續進行部署。

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
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

      ```

   ![設定分層和存留時間屬性](./media/how-to-store-data-blob/iotedge_custom_module.png)

   如需設定分層和 TTL 部署模組之後，請參閱[編輯模組對應項](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)。 如需有關所需屬性的詳細資訊，請參閱[定義或更新所需屬性](module-composition.md#define-or-update-desired-properties)。

1. 選取 [ **儲存**]。

1. 選取 [下一步]  繼續前往路由區段。

#### <a name="specify-routes"></a>指定路由

保留預設路由，並選取**下一步**繼續檢閱一節。

#### <a name="review-deployment"></a>檢閱部署

檢閱區段會顯示 JSON 部署資訊清單，該清單會根據您在前兩個區段中的選項而建立。 另外還有兩個模組宣告您未新增︰ **$edgeAgent**並 **$edgeHub**。 這兩個模組組成 [IoT Edge 執行階段](iot-edge-runtime.md)，在每個部署中都是必要的預設值。

檢閱您的部署資訊，然後選取 [提交]  。

### <a name="verify-your-deployment"></a>驗證您的部署

提交部署之後，您會返回您 IoT 中樞的 [IoT Edge]  頁面。

1. 選取您使用部署設定為目標的 IoT Edge 裝置，以開啟其詳細資料。
1. 在裝置詳細資料中，確認 Blob 儲存體模組列為 [指定於部署中]  和 [由裝置回報]  。

模組在裝置上啟動然後向 IoT 中樞回報可能需要一點時間。 重新整理頁面來查看更新狀態。

## <a name="deploy-from-visual-studio-code"></a>從 Visual Studio Code 部署

Azure IoT Edge 提供 Visual Studio Code 中的範本協助您開發解決方案。 若要使用 blob 儲存體模組中建立新的 IoT Edge 方案，並設定部署資訊清單，請使用下列步驟。

1. 選取 [檢視]   > [命令選擇區]  。

1. 在命令選擇區中，輸入並執行命令 Azure IoT Edge：  新增 IoT Edge 解決方案。

   ![執行新的 IoT Edge 解決方案](./media/how-to-develop-csharp-module/new-solution.png)

   依照命令選擇區中的提示建立解決方案。

   | 欄位 | 值 |
   | ----- | ----- |
   | 選取資料夾 | 選擇您的 Visual Studio 程式碼，以建立方案檔的開發電腦上的位置。 |
   | 提供解決方案名稱 | 輸入解決方案的描述性名稱或接受預設值 **EdgeSolution**。 |
   | 選取模組範本 | 選擇 [現有模組 (輸入完整映像 URL)]  。 |
   | 提供模組名稱 | 為您的模組輸入全部小寫名稱，例如 **azureblobstorage**。<br /><br />針對 IoT Edge 模組上的 Azure Blob 儲存體，請務必使用小寫名稱。 IoT Edge 在參考模組時會區分大小寫，且儲存體 SDK 預設為小寫。 |
   | 提供模組的 Docker 映像 | 提供映像 URI：**mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code 會採用您提供的資訊、建立 IoT Edge 解決方案，然後將其載入至新的視窗。 解決方案範本會建立部署資訊清單範本，其中包含 Blob 儲存體模組映像，但是您需要設定模組的建立選項。

1. 在新的解決方案工作區中開啟 [deployment.template.json]  ，並找出 [模組]  區段。 進行下列設定變更：

   1. 刪除這次部署不需要的 **tempSensor** 模組。

   1. 複製並貼上下列程式碼插入`createOptions`欄位：

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![更新模組 createOptions-Visual Studio Code](./media/how-to-store-data-blob/create-options.png)

1. 將 `<your storage account name>` 取代為您可以記住的名稱。 帳戶名稱應該是 3 到 24 個字元，包含小寫字母和數字。 不能是空格。

1. 使用 64 位元組 base64 金鑰取代 `<your storage account key>`。 您可以 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 之類的工具產生金鑰。 您將使用這些認證，從其他模組存取 Blob 儲存體。

1. 取代`<storage directory bind>`根據您容器的作業系統。 提供[磁碟區](https://docs.docker.com/storage/volumes/)的名稱，或您想要 Blob 模組儲存其資料的 IoT Edge 裝置目錄絕對路徑。 儲存體目錄繫結會對應模組中您提供要設定位置的裝置。  

      - 適用於 Linux 容器的格式是 *\<儲存體路徑 >: / blobroot*。 例如， **/srv/containerdata: / blobroot**或是**my 磁碟區: / blobroot**。
      - 格式是適用於 Windows 容器 *\<儲存體路徑 >: C: / BlobRoot*。 例如， **c: / ContainerData:C: / BlobRoot**或是**我-磁碟區： C: / blobroot**。

      > [!IMPORTANT]
      > 請勿變更儲存體目錄繫結的後半段 (指向模組中的特定位置)。 儲存體目錄繫結的結尾對於 Linux 容器應一律為 **:/blobroot**，對於 Windows 容器應一律為 **:C:/BlobRoot**。

1. 設定[分層](how-to-store-data-blob.md#tiering-properties)並[存留時間](how-to-store-data-blob.md#time-to-live-properties)您的模組，新增以下 JSON 屬性*deployment.template.json*檔案。 設定每個屬性，使用適當的值，然後儲存檔案。

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

   ![設定所需的屬性，如 azureblobstorageoniotedge-Visual Studio Code](./media/how-to-store-data-blob/tiering_ttl.png)

   如需設定分層和 TTL 部署模組之後，請參閱[編輯模組對應項](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)。 如需有關容器建立選項、 重新啟動原則，以及所需的狀態，請參閱[EdgeAgent 的所需屬性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。

1. 儲存 *deployment.template.json* 檔案。

1. 以滑鼠右鍵按一下 **deployment.template.json**，然後選取 [產生 IoT Edge 部署資訊清單]  。

1. Visual Studio 程式碼會採用您在中提供的資訊*deployment.template.json*並使用它來建立新的部署資訊清單檔。 隨即在解決方案工作區新 **config** 資料夾中建立部署資訊清單。 您有該檔案時，就可以依照[從 Visual Studio Code 部署 Azure IoT Edge 模組](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模組](how-to-deploy-modules-cli.md)中的步驟進行。

## <a name="deploy-multiple-module-instances"></a>部署多個模組執行個體

如果您想要部署的 Azure Blob 儲存體，IoT Edge 模組上的多個執行個體，您需要提供不同的儲存體路徑，並將變更`HostPort`模組繫結至的值。 Blob 儲存體模組一律會公開容器的 11002 連接埠，但是您可以宣告哪個連接埠繫結至主機。

編輯**容器建立選項**（在 Azure 入口網站中） 或**createOptions**欄位 (在*deployment.template.json* Visual Studio Code 中的檔案) 來變更`HostPort`值：

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

您連接到其他 Blob 儲存體模組時，請變更端點來指向已更新的主機連接埠。

## <a name="next-steps"></a>後續步驟

如需部署資訊清單的功能，以及如何建立此類清單的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md) (英文)。
