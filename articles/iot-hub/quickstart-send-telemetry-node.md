---
title: 將遙測傳送至 Azure IoT 中樞快速入門 (Node.js) | Microsoft Docs
description: 本快速入門中，您會執行兩個範例 Node.js 應用程式，以傳送模擬遙測至 IoT 中樞以及從 IoT 中樞讀取遙測，以便在雲端中處理。
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: node
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: 0be67b936780d68198a00cb99529643d453621e3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-nodejs"></a>快速入門：將遙測從裝置傳送至 IoT 中樞，並使用後端應用程式從中樞讀取遙測 (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT 中樞是一項 Azure 服務，可讓您從 IoT 裝置將大量的遙測擷取到雲端進行儲存或處理。 在此快速入門中，您透過 IoT 中樞，將遙測從模擬裝置應用程式傳送到後端應用程式以進行處理。

本快速入門會使用兩個預先撰寫的 Node.js 應用程式，一個用來傳送遙測，而另一個用來從中樞讀取遙測。 在執行下列兩個應用程式之前，您需要建立一個 IoT 中樞，並向中樞註冊一個裝置。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>先決條件

您在此快速入門中執行的兩個範例應用程式是使用 Node.js 所撰寫的。 您的開發電腦上需要 Node.js 4.x.x 版或更高版本。

您可以從 [nodejs.org](https://nodejs.org) 下載適用於多種平台的 Node.js。

您可以使用下列命令，以確認開發電腦上目前的 Node.js 版本：

```cmd/sh
node --version
```

從 https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip 下載範例 Node.js 專案並將 ZIP 封存檔解壓縮。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure CLI 來註冊模擬的裝置。

1. 新增 IoT 中樞 CLI 擴充功能，並建立裝置身分識別。 使用您為 IoT 中樞所選擇的名稱來取代 `{YourIoTHubName}`：

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

    如果您為裝置選擇不同的名稱，請先在範例應用程式中更新該裝置名稱，再執行應用程式。

1. 執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    記下裝置連接字串，它看似 `Hostname=...=`。 您稍後會在快速入門中使用此值。

1. 您也需要_服務連接字串_，讓後端應用程式能夠連線到您的 IoT 中樞並擷取訊息。 下列命令可擷取 IoT 中樞的服務連接字串：

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    記下服務連接字串，它看似 `Hostname=...=`。 您稍後會在快速入門中使用此值。 服務連接字符串與裝置連接字串不同。

## <a name="send-simulated-telemetry"></a>傳送模擬的遙測

模擬裝置應用程式會連線到 IoT 中樞上的裝置特定端點，並且傳送模擬的溫度和溼度遙測。

1. 在終端機視窗中，瀏覽至範例 Node.js 專案的根資料夾。 然後瀏覽至 **Quickstarts\simulated-device** 資料夾。

1. 在您選擇的文字編輯器中開啟 **SimulatedDevice.js** 檔案。

    使用先前所記錄的裝置連接字串來取代 `connectionString` 變數的值。 然後將變更儲存到 **SimulatedDevice.js** 檔案。

1. 在終端機視窗中，執行下列命令以安裝模擬的裝置應用程式所需的程式庫並執行模擬的裝置應用程式：

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![執行模擬的裝置](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>從您的中樞讀取遙測

後端應用程式會連線到 IoT 中樞上的服務端**事件**端點。 應用程式會接收模擬裝置所傳送的「裝置到雲端」訊息。 IoT 中樞後端應用程式通常在雲端中執行，以接收和處理「裝置到雲端」訊息。

1. 在另一個終端機視窗中，瀏覽至範例 Node.js 專案的根資料夾。 然後瀏覽至 **read-d2c-messages** 資料夾。

1. 在您選擇的文字編輯器中開啟 **Quickstarts\ReadDeviceToCloudMessages.js** 檔案。

    使用先前所記錄的服務連接字串來取代 `connectionString` 變數的值。 然後將您的變更儲存到 **ReadDeviceToCloudMessages.js** 檔案。

1. 在終端機視窗中，執行下列命令安裝所需的程式庫並執行後端應用程式：

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    下列螢幕擷取畫面顯示由模擬裝置傳送遙測至中樞時，後端應用程式接收遙測的輸出：

    ![執行後端應用程式](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續完成下一個快速入門，請保留資源群組和 IoT 中樞，以供稍後重複使用。

如果您不再需要 IoT 中樞，請在入口網站中刪除它和資源群組。 若要刪除，請選取包含 IoT 中樞的 **qs-iot-hub-rg** 資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已經設定 IoT 中樞、註冊裝置、使用 Node.js 應用程式將模擬的遙測傳送到中樞，並使用簡單的後端應用程式從中樞讀取遙測。

若要了解如何從後端應用程式控制您的模擬裝置，請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [快速入門：控制連線到 IoT 中樞的裝置](quickstart-control-device-node.md)