---
title: 教學課程-使用 Azure IoT 中樞 message 擴充
description: 示範如何針對 Azure IoT 中樞訊息使用 message 擴充的教學課程
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 77d900844705bb86ce4bcfeda31d6ee765cb8d45
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535002"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>教學課程：使用 Azure IoT 中樞 message 擴充 (預覽)

*Message 擴充*是 IoT 中樞在訊息傳送至指定的端點之前, 以其他資訊來*戳記*訊息的能力。 使用 message 擴充的其中一個原因是要包含可用於簡化下游處理的資料。 例如, 使用裝置對應項標記來擴充裝置遙測訊息, 可以降低客戶的負載, 讓裝置對應項 API 呼叫此資訊。 如需詳細資訊, 請參閱[message 擴充的總覽](iot-hub-message-enrichments-overview.md)。

在本教學課程中, 您會使用 Azure CLI 來設定資源, 包括兩個指向兩個不同儲存體容器的端點- -擴充和**原始**。 然後, 您可以使用[Azure 入口網站](https://portal.azure.com), 將 message 擴充設定為只套用至使用擴充的儲存體容器傳送至端點的訊息。 您會將訊息傳送至 IoT 中樞, 並將其路由至兩個儲存體容器。 只有傳送至擴充儲存容器端點的訊息才會擴充。

若要完成本教學課程, 您將執行下列工作:

**使用 IoT 中樞 message 擴充**
> [!div class="checklist"]
> * 使用 Azure CLI 建立資源--IoT 中樞、具有兩個端點的儲存體帳戶, 以及路由設定。
> * 使用 Azure 入口網站來設定 message 擴充。
> * 執行模擬 IoT 裝置的應用程式, 將訊息傳送至中樞。
> * 查看結果, 並確認訊息擴充如預期般運作。

## <a name="prerequisites"></a>必要條件

* 您必須擁有 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

* 安裝 [Visual Studio](https://www.visualstudio.com/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>取出範例程式碼

下載[IoT 裝置模擬](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)並將其解壓縮。 此存放庫中有數個應用程式, 包括您將用來將訊息傳送至 IoT 中樞的其中一個。

此下載也包含用來建立用來測試訊息擴充之資源的腳本。 腳本位於/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli。 現在, 您可以查看腳本並加以使用。 您也可以直接從文章複製腳本。

當您準備好開始測試時, 您將使用此下載中的裝置模擬應用程式, 將訊息傳送至 IoT 中樞。

## <a name="set-up-and-configure-resources"></a>安裝和設定資源

除了建立必要的資源之外, Azure CLI 腳本也會將兩個路由設定為個別儲存體容器的端點。 如需設定路由的詳細資訊, 請參閱[路由教學](tutorial-routing.md)課程。 設定資源之後, 您可以使用[Azure 入口網站](https://portal.azure.com)來為每個端點設定 message 擴充, 然後繼續進行測試步驟。

> [!NOTE]
> 所有訊息都會路由傳送至這兩個端點, 但只有使用已設定之 message 擴充的訊息才會擴充。
>

您可以使用下列腳本, 或在下載的存放庫的/resources 資料夾中開啟腳本。 以下是腳本將執行的步驟:

* 建立 IoT 中樞。
* 建立儲存體帳戶。
* 在儲存體帳戶中建立兩個容器: 一個用於擴充的訊息, 另一個用於未擴充的訊息。
* 設定兩個不同儲存體帳戶的路由。
    * 建立每個儲存體帳戶容器的端點。
    * 建立每個儲存體帳戶容器端點的路由。

有數個資源名稱必須是全域唯一的，例如 IoT 中樞名稱和儲存體帳戶名稱。 為了讓腳本更容易執行, 這些資源名稱會附加一個隨機的英數位元值, 稱為*randomValue*。 randomValue 會在指令碼頂端產生一次，並於需要時加到整個指令碼的資源名稱後面。 如果您不想使用隨機值，則可將其設定為空字串，或設定為特定值。

如果您尚未這麼做, 請開啟 Bash 的[Cloud Shell 視窗](https://shell.azure.com)。 在解壓縮的存放庫中開啟腳本, 使用 Ctrl-A 來選取它, 然後按 Ctrl-C 加以複製。 或者, 您可以複製下列 CLI 腳本, 或直接在 cloud shell 中開啟它。 以滑鼠右鍵按一下命令列, 然後選取 [**貼**上], 將腳本貼到 Azure cloud shell 視窗中。 腳本會一次執行一個語句。 在腳本停止執行之後, 請選取**Enter**以確定它會執行最後一個命令。 下列程式碼區塊會顯示所使用的腳本, 並加上批註來說明它所執行的作業。

以下是腳本所建立的資源。 擴充表示資源適用于具有擴充的訊息。 **原始**表示資源適用于未擴充的訊息。

| Name | 值 |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 容器名稱 | 來源語言  |
| 容器名稱 | 豐富  |
| IoT 裝置名稱 | Contoso-測試-裝置 |
| IoT 中樞名稱 | ContosoTestHubMsgEn |
| 儲存體帳戶名稱 | contosostorage |
| 端點名稱1 | ContosoStorageEndpointOriginal |
| 端點名稱2 | ContosoStorageEndpointEnriched|
| 路由名稱1 | ContosoStorageRouteOriginal |
| 路由名稱2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
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

此時, 所有資源都已設定完成, 並已設定路由。 您可以在入口網站中查看訊息路由設定, 並針對傳送至擴充儲存體容器的訊息設定訊息擴充。

### <a name="view-routing-and-configure-the-message-enrichments"></a>查看路由和設定訊息擴充

1. 選取 [**資源群組**], 然後選取在此教學課程中設定的資源群組 (**ContosoResources_MsgEn**), 以移至您的 IoT 中樞。 在清單中尋找 IoT 中樞並加以選取。 為 Iot 中樞選取 [*訊息路由*]。

   ![選取訊息路由](./media/tutorial-message-enrichments/select-iot-hub.png)

   [訊息路由] 窗格有三個索引標籤:**路由**、**自訂端點**和擴充**訊息**。 您可以流覽前兩個索引標籤, 以查看腳本所設定的設定。 使用第三個索引標籤來新增訊息擴充。 讓我們針對稱為「擴充」的儲存體容器, 擴充前往端點的訊息。 填寫 [名稱] 和 [值], 然後從下拉式清單中選取端點**ContosoStorageEndpointEnriched** 。 以下是設定擴充的範例, 其會將 IoT 中樞名稱新增至訊息:

   ![新增第一個擴充](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 將這些值新增至 ContosoStorageEndpointEnriched 端點的清單。

   | Name | 值 | 端點 (下拉式清單) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Id | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 如果您的裝置沒有對應項, 您在這裡放入的值將會被標記為 [訊息] 擴充中值的字串。 若要查看裝置對應項資訊, 請在入口網站中移至您的中樞, 然後選取 [ **IoT 裝置**], 選取您的裝置, 然後選取頁面頂端的 [**裝置**對應項]。
   >
   > 您可以編輯對應項資訊來新增標記 (例如 location), 並視需要將它設定為特定值。 如需詳細資訊，請參閱[了解和使用 Azure IoT 中樞的裝置對應項](iot-hub-devguide-device-twins.md)

3. 當您完成時, 您的窗格看起來應該如下圖所示:

   ![已新增所有擴充的資料表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 選取 [套用] 以儲存變更。

## <a name="send-messages-to-the-iot-hub"></a>將訊息傳送至 IoT 中樞

既然已針對端點設定了 message 擴充, 請執行模擬裝置應用程式, 將訊息傳送至 IoT 中樞。 中樞已設定完成下列設定:

* 路由至儲存體端點 ContosoStorageEndpointOriginal 的訊息將不會擴充, 且會儲存在儲存體容器`original`中。

* 路由傳送至儲存體端點 ContosoStorageEndpointEnriched 的訊息將會擴充並儲存在儲存體容器`enriched`中。

模擬裝置應用程式是解壓縮下載中的其中一個應用程式。 應用程式會在[路由教學](tutorial-routing.md)課程中, 針對每個不同的訊息路由方法傳送訊息。這包括 Azure 儲存體。

對解決方案檔案 (IoT_SimulatedDevice.sln) 按兩下以在 Visual Studio 中開啟程式碼，然後開啟 Program.cs。 以`{your hub name}` IoT 中樞名稱取代。 IoT 中樞主機名稱的格式為 **{您的中樞名稱}. azure-devices.net**。 在本教學課程中, 中樞主機名稱是**ContosoTestHubMsgEn.azure-devices.net**。 接下來, `{device key}`使用您稍早在執行腳本來建立資源時所儲存的裝置金鑰來取代。

如果您沒有裝置金鑰, 您可以從入口網站取得它。 登入之後, 移至 [**資源群組**], 選取您的資源群組, 然後選取您的 IoT 中樞。 查看測試裝置的 [ **IoT 裝置**], 然後選取您的裝置。 選取 [**主要金鑰**] 旁邊的複製圖示, 將它複製到剪貼簿。

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>執行和測試

執行主控台應用程式。 請等待數分鐘。 正在傳送的訊息會顯示在應用程式的主控台畫面上。

應用程式每一秒就會將最新裝置到雲端的訊息傳送至 IoT 中樞。 訊息包含 JSON 序列化的物件與裝置識別碼、溫度、溼度和訊息層級 (預設位置為 `normal`)。 它會隨機指派`critical`或`storage`的層級, 導致訊息路由傳送至儲存體帳戶或預設端點。 傳送至儲存體帳戶中擴充容器的訊息將會擴充。

傳送數個儲存體訊息之後, 請查看資料。

1. 選取 [**資源群組**], 然後尋找您的資源群組 (ContosoResourcesMsgEn) 並加以選取。

2. 選取您的儲存體帳戶 (contosostorage)。 然後從左側的 [選取範圍] 窗格中選取 [**儲存體總管 (預覽)** ]。

   ![選取儲存體 explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   選取 [ **BLOB 容器**], 以查看可使用的兩個容器。

   ![查看儲存體帳戶中的容器](./media/tutorial-message-enrichments/show-blob-containers.png)

名為「擴充」的容器中的訊息會在訊息中包含訊息擴充。 容器中的訊息 (稱為「**原始**」) 將會有未經擴充的原始訊息。 向下切入到其中一個容器, 直到您前往底部並開啟最新的訊息檔案, 然後對另一個容器執行相同動作, 以確認該容器中的訊息未新增任何擴充。

當您查看已擴充的訊息時, 您應該會看到 [我的 IoT 中樞] 具有中樞名稱, 以及 [位置] 和 [客戶識別碼], 如下所示:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

以下是 unenriched 訊息。 「我的 IoT 中樞」、「devicelocation」和「customerID」不會顯示在這裡, 因為此端點沒有擴充。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>清除資源

如果您想要移除您在本教學課程中建立的所有資源, 請刪除資源群組。 此動作會同時刪除群組內含的所有資源。 在本例中，此動作會移除 IoT 中樞、儲存體帳戶和資源群組本身。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 來清除資源

若要移除資源群組，請使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 命令。 在本教學課程剛開始時，`$resourceGroup` 已設回 **ContosoResources**。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本教學課程中, 您已使用下列步驟來設定和測試將 message 擴充新增至 IoT 中樞訊息:

**使用 IoT 中樞 message 擴充**
> [!div class="checklist"]
> * 使用 Azure CLI 建立資源--IoT 中樞、具有兩個 enendpoints 的儲存體帳戶, 以及路由設定。
> * 使用 Azure 入口網站來設定 message 擴充。
> * 執行可模擬 IoT 裝置傳送訊息至中樞的應用程式。
> * 查看結果, 並確認訊息擴充如預期般運作。

如需有關 message 擴充的詳細資訊, 請參閱[message 擴充的總覽](iot-hub-message-enrichments-overview.md)。

如需訊息路由的詳細資訊, 請參閱下列文章:

* [使用 IoT 中樞訊息路由將裝置到雲端訊息傳送至不同的端點](iot-hub-devguide-messages-d2c.md)

* [教學課程：IoT 中樞路由](tutorial-routing.md)