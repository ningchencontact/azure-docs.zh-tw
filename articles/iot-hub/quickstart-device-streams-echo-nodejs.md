---
title: 透過 IoT 中樞裝置串流與使用 Node.js 的裝置應用程式進行通訊 (預覽) | Microsoft Docs
description: 在本快速入門中，您將會執行透過裝置串流與 IoT 裝置進行通訊的 Node.js 服務端應用程式。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c7257ec35f9a53f84edebd5e15b7144c49daf682
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514947"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>快速入門：透過 IoT 中樞裝置串流與使用 Node.js 的裝置應用程式進行通訊 (預覽)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT 中樞目前支援裝置串流作為[預覽功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[IoT 中樞裝置串流](./iot-hub-device-streams-overview.md)可讓服務和裝置應用程式以安全且便於設定防火牆的方式進行通訊。 在公開預覽期間，Node.js SDK 僅支援服務端上的裝置串流。 因此，本快速入門僅提供執行服務端應用程式的指示。 您應執行來自下列其中一個快速入門中的隨附裝置端應用程式：

* [透過 IoT 中樞裝置串流與使用 C 的裝置應用程式進行通訊](./quickstart-device-streams-echo-c.md)

* [透過 IoT 中樞裝置串流與使用 C# 的裝置應用程式進行通訊](./quickstart-device-streams-echo-csharp.md)。

本快速入門中的服務端 Node.js 應用程式具有下列功能：

* 建立對 IoT 裝置的裝置串流。

* 從命令列中讀取輸入，並將其傳送至裝置應用程式，繼而從該處傳回回應。

程式碼將示範裝置串流的起始程序，以及如何用它來傳送和接收資料。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

裝置串流的預覽版目前僅支援在下列區域建立的 IoT 中樞：

*  **美國中部**

*  **美國中部 EUAP**

若要執行本快速入門中的服務端應用程式，您的開發電腦上需要 Node.js 10.x.x 版或更新版本。

您可以從 [Nodejs.org](https://nodejs.org) \(英文\) 下載適用於多種平台的 Node.js。

您可以使用下列命令，以確認開發電腦上目前的 Node.js 版本：

```cmd/sh
node --version
```

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

如果您尚未這樣做，請從 https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip 下載範例 Node.js 專案並將 ZIP 封存檔解壓縮。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-node.md)，則可以略過此步驟。

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>註冊裝置

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-node.md)，則可以略過此步驟。

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. 您也需要*服務連接字串*，讓後端應用程式能夠連線到您的 IoT 中樞並擷取訊息。 下列命令可擷取 IoT 中樞的服務連接字串：

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    請記下所傳回的服務連接字串，以供稍後在本快速入門中使用。 看起來會像下列範例：

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>透過裝置串流在裝置與服務之間進行通訊

在本節中，您會執行裝置端應用程式和服務端應用程式，並在兩者之間進行通訊。

### <a name="run-the-device-side-application"></a>執行裝置端應用程式

如先前所說明，IoT 中樞 Node.js SDK 僅支援服務端上的裝置串流。 對於裝置端應用程式，請使用可在這些快速入門中取得的其中一個隨附裝置程式：

   * [透過 IoT 中樞裝置串流與使用 C 的裝置應用程式進行通訊](./quickstart-device-streams-echo-c.md)

   * [透過 IoT 中樞裝置串流與使用 C# 的裝置應用程式進行通訊](./quickstart-device-streams-echo-csharp.md)

請確定裝置端應用程式已在執行中，再繼續進行下一個步驟。

### <a name="run-the-service-side-application"></a>執行服務端應用程式

假設裝置端應用程式正在執行中，請在本機終端視窗中依照下列步驟執行使用 Node.js 的服務端應用程式：

* 提供您的服務認證和裝置識別碼，作為環境變數。
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   變更 ServiceConnectionString 預留位置以符合您的服務連接字串，如果您提供不同的名稱，則變更 **MyDevice**  以符合您的裝置識別碼。

* 在解壓縮的專案資料夾中瀏覽至 `Quickstarts/device-streams-service`，並使用節點執行範例。

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

在最後一個步驟結束時，服務端程式將會對您的裝置起始串流，且在建立之後，會透過串流將字串緩衝區傳送至服務。 在此範例中，服務端程式會直接讀取終端機上的 `stdin`，並將它傳送至裝置，而裝置隨後會回應它。 這示範了兩個應用程式之間成功的雙向通訊。

![服務端主控台輸出](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

接著，您可以再次 Enter 鍵以終止程式。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會設定 IoT 中樞、註冊裝置、在裝置和服務端建立應用程式之間的裝置串流，以及使用串流在應用程式之間來回傳送資料。

使用以下連結深入了解裝置串流：

> [!div class="nextstepaction"]
> [裝置串流概觀](./iot-hub-device-streams-overview.md) 