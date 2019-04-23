---
title: Azure IoT 中樞裝置串流 C# 快速入門 (預覽) | Microsoft Docs
description: 在本快速入門中，您將會執行兩個範例 C# 應用程式，並透過以 IoT 中樞建立的裝置串流進行通訊。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 2853bd5539a40e3b38927f619756fe37a4cec984
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006869"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>快速入門：透過 IoT 中樞裝置串流與使用 C# 的裝置應用程式進行通訊 (預覽)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT 中樞目前支援裝置串流作為[預覽功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[IoT 中樞裝置串流](./iot-hub-device-streams-overview.md)可讓服務和裝置應用程式以安全且便於設定防火牆的方式進行通訊。 本快速入門會用到兩個利用裝置串流來回傳送資料 (回應) 的 C# 程式。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

裝置串流的預覽版目前僅支援在下列區域建立的 IoT 中樞：

  - **美國中部**
  - **美國中部 EUAP**

您在此快速入門中執行的兩個範例應用程式是使用 C# 所撰寫的。 您的開發電腦上需要有 .NET Core SDK 2.1.0 或更高版本。

您可以從 [.NET](https://www.microsoft.com/net/download/all) 下載適用於多種平台的 .NET Core SDK。

您可以使用下列命令，以確認開發電腦上目前的 C# 版本：

```
dotnet --version
```

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

從 https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip 下載範例 C# 專案並將 ZIP 封存檔解壓縮。 您在裝置端和服務端都會用到它。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyDevice**：這是為已註冊裝置指定的名稱。 使用所示的 MyCDevice。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    記下裝置連接字串，如下列範例所示：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

3. 您也需要 IoT 中樞的_服務連接字串_，讓服務端應用程式能夠連線到您的 IoT 中樞並建立裝置串流。 下列命令會擷取您 IoT 中樞的這個值：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    記下顯示如下的傳回值：

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>透過裝置串流在裝置與服務之間進行通訊

### <a name="run-the-service-side-application"></a>執行服務端應用程式

在解壓縮的專案資料夾中，瀏覽至 `iot-hub/Quickstarts/device-streams-echo/service`。 您必須備妥下列資訊：

| 參數名稱 | 參數值 |
|----------------|-----------------|
| `ServiceConnectionString` | 提供 IoT 中樞的服務連接字串。 |
| `DeviceId` | 提供您先前建立之裝置的識別碼，例如 MyDevice。 |

編譯並執行程式碼，如下所示：

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> 如果裝置端應用程式未及時回應，就會發生逾時。

### <a name="run-the-device-side-application"></a>執行裝置端應用程式

在解壓縮的專案資料夾中，瀏覽至 `iot-hub/Quickstarts/device-streams-echo/device` 目錄。 您必須備妥下列資訊：

| 參數名稱 | 參數值 |
|----------------|-----------------|
| `DeviceConnectionString` | 提供 IoT 中樞的裝置連接字串。 |

編譯並執行程式碼，如下所示：

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

在最後一個步驟結束時，服務端程式將會對您的裝置起始串流，且在建立之後，會透過串流將字串緩衝區傳送至服務。 在此範例中，服務端程式會直接將相同的資料傳回給裝置，而示範兩個應用程式之間成功的雙向通訊。 請參閱下圖。

裝置端上的主控台輸出：![替代文字](./media/quickstart-device-streams-echo-csharp/device-console-output.png "裝置端上的主控台輸出")

服務端上的主控台輸出：![替代文字](./media/quickstart-device-streams-echo-csharp/service-console-output.png "服務端上的主控台輸出")

透過串流傳送的流量將經由 IoT 中樞輸送，而不是直接傳送。 此做法有[這些優點](./iot-hub-device-streams-overview.md#benefits)。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已設定 IoT 中樞、註冊裝置、在裝置和服務端建立 C# 應用程式之間的裝置串流，以及使用串流在應用程式之間來回傳送資料。

使用下列連結深入了解裝置串流：

> [!div class="nextstepaction"]
> [裝置串流概觀](./iot-hub-device-streams-overview.md)
