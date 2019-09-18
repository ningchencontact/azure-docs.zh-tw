---
title: 與連線至 Azure IoT 解決方案的 IoT 隨插即用預覽版裝置互動 | Microsoft Docs
description: 使用 Node.js 連線至已連接 Azure IoT 解決方案的 IoT 隨插即用預覽版裝置，並與其互動。
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: a082e4b7896b317bf2b28971d3693bada95a3445
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806553"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>快速入門：與已連線至解決方案的 IoT 隨插即用預覽版裝置互動

IoT 隨插即用預覽版讓您無須具備基礎裝置實作的知識，即可與裝置的功能互動，而使 IoT 得以簡化。 本快速入門說明如何使用 Node.js 連線至您的解決方案，並控制已連線至解決方案的 IoT 隨插即用裝置。

## <a name="prerequisites"></a>必要條件

從 [nodejs.org](https://nodejs.org) 下載並安裝 Node.js。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>準備 IoT 中樞

您的 Azure 訂用帳戶中也必須要有 Azure IoT 中樞，才能完成本快速入門。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

> [!NOTE]
> 在公開預覽期間，IoT 隨插即用功能只能在**美國中部**、**歐洲北部**和**日本東部**區域中建立的 IoT 中樞上使用。

新增適用於 Azure CLI 的 Microsoft Azure IoT 延伸模組：

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

執行下列命令，在 IoT 中樞建立裝置身分識別。 請將 **YourIoTHubName** 和 **YourDevice** 取代為您的實際名稱。 如果您沒有 IoT 中樞，請依照[下列指示](../iot-hub/iot-hub-create-using-cli.md)建立一個：

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

執行下列命令，以取得中樞的 _IoT 中樞連接字串_：

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>連接裝置

在本快速入門中，您會使用以 Node.js 撰寫的範例環境感應器，作為 IoT 隨插即用裝置。 下列指示說明如何安裝和執行裝置：

1. 複製 GitHub 存放庫：

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. 在終端機中，移至複製存放庫的根資料夾，瀏覽至 **/azure-iot-samples-node/digital-twins/Quickstarts/Device** 資料夾，然後執行下列命令以安裝所有相依性：

    ```cmd/sh
    npm install
    ```

1. 設定_裝置連接字串_：

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. 使用下列命令來執行範例：

    ```cmd/sh
    node sample_device.js
    ```

1. 您會看到訊息，指出裝置已傳送遙測資料及其屬性。 裝置現在已可接收命令和屬性更新。 請勿關閉此終端機，稍後您將需要用它來確認服務範例也已正常運作。

## <a name="build-the-solution"></a>建立解決方案

在本快速入門中，您會使用 Node.js 中的範例 IoT 解決方案，與範例裝置進行互動。

1. 開啟另一個終端機。 移至複製存放庫的資料夾，並瀏覽至 **/azure-iot-samples-node/digital-twins/Quickstarts/Service** 資料夾。 執行下列命令以安裝所有相依性：

    ```cmd/sh
    npm install
    ```

1. 設定_中樞連接字串_：

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>讀取屬性

1. 當您在終端機中連接裝置時，您會看到下列訊息：

    ```cmd/sh
    reported state property as online
    ```

1. 開啟檔案 **get_digital_twin.js**。 將 `deviceID` 取代為您的裝置識別碼，並儲存檔案。

1. 移至您開啟以執行服務範例的終端機，並執行下列命令：

    ```cmd/sh
    node get_digital_twin.js
    ```

1. 在輸出中的 _environmentalSensor_ 元件下方，您會看到相同的狀態已報告：

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>更新可寫入屬性

1. 開啟檔案 **update_digital_twin_property.js**。

1. 在檔案的開頭處，有一組以大寫預留位置定義的常數。 請將 **deviceID** 取代為您實際的裝置識別碼，並使用下列值更新常數，然後儲存檔案：

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. 移至您開啟以執行服務範例的終端機，並使用下列命令執行範例：

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. 在終端機中，您會看到與您的裝置相關聯的數位對應項資訊。 尋找元件 _environmentalSensor_，您會看到新的亮度值 60。

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
            }
          },
          "state": {
            "reported": {
              "value": "online"
            }
          }
        }
      }
    ```

1. 移至您的_裝置_終端機，您會看到裝置已接收更新：

    ```cmd/sh
    Received an update for brightness: 60
    updated the property
    ```
2. 回到您的_服務_終端機，並重新執行下列命令，以確認屬性已更新。
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. 在輸出中的 environmentalSensor 元件下方，您會看到更新的亮度值已報告。 注意：裝置可能需要一段時間才能完成更新。 您可以重複此步驟，直到裝置實際處理屬性更新為止。
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>叫用命令

1. 開啟檔案 **invoke_command.js**。

1. 在檔案開頭處，將 `deviceID` 取代為您的實際裝置識別碼。 使用下列值更新常數，然後儲存檔案：

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. 移至您開啟以執行服務範例的終端機。 請使用下列命令執行此範例：

    ```cmd/sh
    node invoke_command.js
    ```

1. 作業成功時，終端機中會出現如下的輸出：

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. 移至_裝置_終端機，您會看到命令已認可：

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續參閱後續的文章，您可以保留在本快速入門中使用的資源。 否則，您可以刪除您為本快速入門建立的資源，以避免產生額外費用。

若要刪除中樞和已註冊的裝置，請使用 Azure CLI 完成下列步驟：

```azurecli-interactive
az group delete --name <Your group name>
```

若只要刪除您已註冊到 IoT 中樞的裝置，請使用 Azure CLI 完成下列步驟：

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 解決方案。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線到裝置並與其互動](howto-develop-solution.md)
