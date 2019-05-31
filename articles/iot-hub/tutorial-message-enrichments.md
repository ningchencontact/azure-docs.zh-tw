---
title: 教學課程-使用 Azure IoT 中樞訊息類
description: 示範如何使用適用於 Azure IoT 中樞訊息的訊息類教學課程
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259070"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>教學課程：使用 Azure IoT 中樞訊息類 （預覽）

*訊息類*是 IoT 中樞的能力*戳記*訊息之前訊息傳送至指定端點的其他資訊。 若要使用訊息類的其中一個原因是包含可用來簡化下游處理的資料。 比方說，豐富的裝置對應項標記的裝置遙測訊息可以減少客戶進行 API 呼叫這項資訊的裝置對應項上的負載。 如需詳細資訊，請參閱 <<c0> [ 概觀訊息類](iot-hub-message-enrichments-overview.md)。

在本教學課程中，您可以使用 Azure CLI 設定資源，包括指向兩個不同的儲存體容器-的兩個端點**豐富**並**原始**。 接著，您使用[Azure 入口網站](https://portal.azure.com)若要設定來只會套用至訊息傳送至端點的訊息類**豐富**儲存體容器。 您將訊息傳送至 IoT 中樞，它會路由傳送至這兩個儲存體容器。 只有訊息傳送至的端點**豐富**會擴充儲存體容器。

以下是完成本教學課程，您將執行的工作：

**使用 IoT 中樞訊息類**
> [!div class="checklist"]
> * 使用 Azure CLI 建立資源--IoT 中樞、 儲存體帳戶有兩個端點和路由組態。
> * 您可以使用 Azure 入口網站來設定訊息類。
> * 執行應用程式，模擬 IoT 裝置將訊息傳送至中樞。
> * 檢視結果，並確認訊息類如預期般運作。

## <a name="prerequisites"></a>必要條件

* 您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

* 安裝 [Visual Studio](https://www.visualstudio.com/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>擷取的範例程式碼

下載[IoT 裝置模擬](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)並將它解壓縮。 此存放庫有數個應用程式，包括您將會的用來將訊息傳送至 IoT 中樞。

本下載也包含用於建立用來測試訊息類的資源指令碼。 指令碼位於 /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli。 現在，您可以查看 指令碼，並使用它。 您也可以直接從本文複製指令碼。

當您準備好開始測試時，您將使用裝置模擬應用程式，從這個下載，若要將訊息傳送至 IoT 中樞。

## <a name="set-up-and-configure-resources"></a>安裝和設定資源

除了建立所需的資源，Azure CLI 指令碼也會設定兩個路由，是個別的儲存體容器的端點。 如需有關如何設定路由的詳細資訊，請參閱 <<c0> [ 路由的教學課程](tutorial-routing.md)。 資源會設定好之後，您會使用[Azure 入口網站](https://portal.azure.com)設定訊息類每個端點，並繼續進行測試的步驟。

> [!NOTE]
> 所有訊息會路由都傳送至這兩個端點，但只將移至設定的訊息類端點的訊息會進行擴充。
>

您可以使用下列指令碼，或下載存放庫的 /resources 資料夾中開啟指令碼。 以下是指令碼會執行的步驟：

* 建立 IoT 中樞。
* 建立儲存體帳戶。
* 建立兩個容器位於儲存體帳戶-一個用於豐富的訊息，一個不豐富的訊息。
* 設定兩個不同的儲存體帳戶的路由。
    * 建立每個儲存體帳戶容器的端點。
    * 建立每個儲存體帳戶容器端點的路由。

有數個資源名稱必須是全域唯一的，例如 IoT 中樞名稱和儲存體帳戶名稱。 若要讓執行指令碼更容易，那些資源的名稱會加上隨機英數字元值呼叫*randomValue*。 randomValue 會在指令碼頂端產生一次，並於需要時加到整個指令碼的資源名稱後面。 如果您不想使用隨機值，則可將其設定為空字串，或設定為特定值。

如果您尚未這麼做，開啟[bash Cloud Shell 視窗](https://shell.azure.com)。 開啟解壓縮的存放庫中的指令碼，請使用 Ctrl-A 可選取全部，則 Ctrl + C 可複製它。 或者，您可以複製下列的 CLI 指令碼，或直接在 cloud shell 中開啟它。 在 [Azure cloud shell] 視窗中貼上指令碼，在命令列上按一下滑鼠右鍵，然後選取**貼上**。 指令碼會執行一個陳述式，一次。 指令碼停止執行之後，請選取**Enter**以確定它執行的最後一個命令。 下列程式碼區塊顯示使用，以及說明它正在進行的註解的指令碼。

以下是指令碼所建立的資源。 **豐富**表示資源的類訊息。 **原始**表示資源不豐富的訊息。

| 名稱 | Value |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 容器名稱 | 原始  |
| 容器名稱 | 擴充  |
| IoT 裝置名稱 | Contoso-Test-Device |
| IoT 中樞名稱 | ContosoTestHubMsgEn |
| 儲存體帳戶名稱 | contosostorage |
| 端點名稱 1 | ContosoStorageEndpointOriginal |
| 端點名稱 2 | ContosoStorageEndpointEnriched|
| 路由名稱 1 | ContosoStorageRouteOriginal |
| 路由名稱 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

此時，資源都已啟動就緒和路由的設定。 您可以在入口網站中檢視訊息的路由組態和設定的訊息移至訊息類**豐富**儲存體容器。

### <a name="view-routing-and-configure-the-message-enrichments"></a>檢視路由及設定訊息類

1. 移至您的 IoT 中樞，方法是選取**資源群組**，然後選取 設定本教學課程中的資源群組 (**ContosoResources_MsgEn**)。 在清單中找到 IoT 中樞，並加以選取。 選取 *訊息路由** 為 Iot 中樞。

   ![選取訊息路由](./media/tutorial-message-enrichments/select-iot-hub.png)

   訊息路由的窗格就會有三個索引標籤-**路由**，**自訂端點**，並**擴充訊息**。 您可以瀏覽看到組態指令碼所設定的前兩個索引標籤。 您可以使用第三個索引標籤來新增訊息類。 讓我們來擴充訊息會進入呼叫的儲存體容器的端點**豐富**。 填寫名稱和值，然後選取 端點**ContosoStorageEndpointEnriched**從下拉式清單中。 以下是設定 IoT 中樞名稱新增至 「 訊息豐富 」 範例：

   ![新增第一個擴充](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 將 ContosoStorageEndpointEnriched 端點清單中的這些值。

   | 名稱 | Value | 端點 （下拉式清單中） |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | 裝置位置 | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 如果您的裝置並沒有對應項，您在這裡放入的值都將會為訊息類中值的字串加上戳記。 若要查看裝置對應項資訊，請移至您在入口網站的中樞，然後選取**IoT 裝置**，選取您的裝置，然後選取**裝置對應項**頁面的頂端。
   >
   > 您可以編輯新增的標記 （例如位置），並將它設定為特定值，如果您想要的對應項資訊。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](iot-hub-devguide-device-twins.md)

3. 當您完成時，您的窗格看起來應該類似此映像：

   ![具有新增的所有類資料表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 選取 **套用**以儲存變更。

## <a name="send-messages-to-the-iot-hub"></a>將訊息傳送至 IoT 中樞

既然訊息類端點，請執行將訊息傳送至 IoT 中樞模擬裝置應用程式設定。 中樞已設定規則，以達成下列目的：

* 將訊息路由傳送至儲存體端點 ContosoStorageEndpointOriginal 不進行擴充，並會儲存在儲存體容器`original`。

* 會擴充並儲存在儲存體容器中將訊息路由傳送至儲存體端點 ContosoStorageEndpointEnriched `enriched`。

模擬裝置應用程式是在解壓縮下載中的應用程式。 應用程式會將訊息傳送的每個在不同的訊息路由方法[路由的教學課程](tutorial-routing.md); 這包括 Azure 儲存體。

對解決方案檔案 (IoT_SimulatedDevice.sln) 按兩下以在 Visual Studio 中開啟程式碼，然後開啟 Program.cs。 替代`{your hub name}`IoT 中樞名稱。 IoT 中樞主機名稱的格式 **{您的中樞名稱}.azure-devices.net**。 本教學課程為中樞主機名稱**ContosoTestHubMsgEn.azure-devices.net**。 接下來，以取代`{device key}`的裝置金鑰儲存稍早時執行指令碼來建立的資源。

如果您沒有裝置的金鑰，您可以從入口網站來進行擷取。 登入之後，請移至**資源群組**選取資源群組中，然後選取您的 IoT 中樞。 查看底下**IoT 裝置**測試裝置，然後選取您的裝置。 選取複製圖示旁**主索引鍵**將它複製到剪貼簿。

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>執行和測試

執行主控台應用程式。 請等待數分鐘。 正在傳送的訊息會顯示在應用程式的主控台畫面上。

應用程式每一秒就會將最新裝置到雲端的訊息傳送至 IoT 中樞。 訊息包含 JSON 序列化的物件與裝置識別碼、溫度、溼度和訊息層級 (預設位置為 `normal`)。 它會將隨機指派的層級`critical`或`storage`，進而導致將訊息路由傳送至儲存體帳戶或預設端點。 若要傳送的訊息**豐富**會擴充儲存體帳戶中的容器。

已傳送數個儲存體訊息之後，檢視資料。

1. 選取 **資源群組**，然後尋找您的資源群組 (ContosoResourcesMsgEn) 並加以選取。

2. 選取您的儲存體帳戶 (contosostorage)。 然後選取**儲存體總管 （預覽）** 從左側的 [選取] 窗格。

   ![選取儲存體總管](./media/tutorial-message-enrichments/select-storage-explorer.png)

   選取  **BLOB 容器**可查看兩個可用的容器。

   ![請參閱儲存體帳戶中的容器](./media/tutorial-message-enrichments/show-blob-containers.png)

在容器中的訊息呼叫**豐富**已包含在訊息中的訊息類。 在容器中的訊息呼叫**原始**會有與沒有類未經處理的訊息。 向下切入至其中一個容器，直到元兇和開啟最新的訊息檔案，然後執行其他容器，以確認沒有任何類加入至該容器中的訊息相同的動作。

當您查看已加強的訊息時，您應該會看到 「 我 IoT 中樞 」 的中樞名稱，以及位置和客戶識別碼，就像這樣：

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

而這裡是 unenriched 的訊息。 「 「 我 IoT 中樞 」，「 裝置位置 」 和"customerID"不會出現在這裡，因為這個端點有沒有類。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>清除資源

如果您想要移除所有您已建立本教學課程中的資源，刪除資源群組。 此動作會同時刪除群組內含的所有資源。 在本例中，此動作會移除 IoT 中樞、儲存體帳戶和資源群組本身。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 來清除資源

若要移除資源群組，請使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。 在本教學課程剛開始時，`$resourceGroup` 已設回 **ContosoResources**。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您可以設定及測試 IoT 中樞的訊息，請使用下列步驟來新增訊息類：

**使用 IoT 中樞訊息類**
> [!div class="checklist"]
> * 使用 Azure CLI 建立資源--IoT 中樞、 儲存體帳戶，具有兩個 enendpoints 和路由組態。
> * 您可以使用 Azure 入口網站來設定訊息類。
> * 執行應用程式，模擬的 IoT 裝置傳送訊息至中樞。
> * 檢視結果，並確認訊息類如預期般運作。

如需訊息類的詳細資訊，請參閱[概觀訊息類](iot-hub-message-enrichments-overview.md)。

如需訊息路由的詳細資訊，請參閱下列文章：

* [使用 IoT 中樞訊息路由來傳送裝置到雲端訊息至不同的端點](iot-hub-devguide-messages-d2c.md)

* [教學課程：路由的 IoT 中樞](tutorial-routing.md)