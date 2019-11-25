---
title: Register a new Azure IoT Edge device | Microsoft Docs
description: 使用 Azure CLI 的 IoT 擴充功能來註冊新的 IoT Edge 裝置並擷取連接字串
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/21/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 793ddcb9f218248c396e10f23201dfe905545ceb
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456845"
---
# <a name="register-an-azure-iot-edge-device"></a>Register an Azure IoT Edge device

Before you can use your IoT devices with Azure IoT Edge, you must register them with your IoT hub. Once a device is registered, you can retrieve a connection string to set up your device for IoT Edge workloads.

You have the choice of registering by using one of the following tools:

* [Azure portal](https://portal.azure.com) provides a graphical user interface to create, view, and manage Azure resources.
* [Visual Studio Code](https://code.visualstudio.com/) is a source-code editor. Azure IoT extensions make it easy to manage IoT resources from the same tool where you're developing IoT solutions.
* [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is a command-line tool for managing Azure resources. Its reusable commands are helpful for automating tasks.

## <a name="register-in-the-azure-portal"></a>Register in the Azure portal

You can perform all registration tasks in the Azure portal.

### <a name="prerequisites-for-the-azure-portal"></a>Prerequisites for the Azure portal

A free or standard [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Create an IoT Edge device in the Azure portal

In your IoT Hub in the Azure portal, IoT Edge devices are created and managed separately from IOT devices that are not edge enabled.

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
2. In the left pane, select **IoT Edge** from the menu.
3. 選取 [新增 IoT Edge 裝置]。
4. 提供描述性裝置識別碼。 Use the default settings to auto-generate authentication keys and connect the new device to your hub.
5. 選取 [儲存]。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>View IoT Edge devices in the Azure portal

所有連線至 IoT 中樞且已啟用 Edge 的裝置都列於 [IoT Edge] 頁面。

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Retrieve the connection string in the Azure portal

您準備好開始設定裝置時，需要一個利用在 IoT 中樞中的身分識別連結實體裝置的連接字串。

1. From the **IoT Edge** page in the portal, click on the device ID from the list of IoT Edge devices.
2. 複製 [連接字串 (主要金鑰)] 或 [連接字串 (次要金鑰)] 的值。

## <a name="register-with-visual-studio-code"></a>Register with Visual Studio Code

目前有多種方式可以在 VS Code 中執行大部分的操作。 This article uses the Explorer, but you can also use the Command Palette to run the steps.

### <a name="prerequisites-for-visual-studio-code"></a>Prerequisites for Visual Studio Code

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>登入以存取您的 IoT 中樞

You can use the Azure IoT extensions for Visual Studio Code to perform operations with your IoT Hub. For these operations to work, you need to sign in to your Azure account and select your IoT Hub.

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。
1. At the bottom of the Explorer, expand the **Azure IoT Hub** section.

   ![展開 [Azure IoT 中樞裝置] 區段](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Click on the **...** in the **Azure IoT Hub** section header. 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選擇 [選取 IoT 中樞]。
1. If you aren't signed in to your Azure account, follow the prompts to do so.
1. 選取 Azure 訂用帳戶。
1. 選取您的 IoT 中樞。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Create an IoT Edge device with Visual Studio Code

1. 在 VS Code 總管中，展開 [Azure IoT 中樞裝置] 區段。
1. 按一下 [Azure IoT 中樞裝置] 區段標題中的 **...** 。 若未看到省略符號，請按一下標題或將滑鼠暫留在其上方。
1. 選取 [建立 IoT Edge 裝置]。
1. 在開啟的文字方塊中，提供裝置的識別碼。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

在輸出畫面中，您會看到命令的結果。 系統會列印裝置資訊，其中包括您提供的 **deviceId** 以及可用來將實體裝置連線到 IoT 中樞的 **connectionString**。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>View IoT Edge devices with Visual Studio Code

All the devices that connect to your IoT hub are listed in the **Azure IoT Hub** section of the Visual Studio Code Explorer. IoT Edge devices are distinguishable from non-Edge devices with a different icon, and the fact that the **$edgeAgent** and **$edgeHub** modules are deployed to each IoT Edge device.

![檢視您的 IoT 中樞的所有 IoT Edge 裝置](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Retrieve the connection string with Visual Studio Code

您準備好開始設定裝置時，需要一個利用在 IoT 中樞中的身分識別連結實體裝置的連接字串。

1. Right-click on the ID of your device in the **Azure IoT Hub** section.
1. 選取 [複製裝置連接字串]。

   連接字串會複製到剪貼簿。

您也可以選取右鍵功能表的 [取得裝置資訊]，以在輸出視窗中查看所有裝置資訊，包括連接字串。

## <a name="register-with-the-azure-cli"></a>Register with the Azure CLI

The [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) is an open-source cross platform command-line tool for managing Azure resources such as IoT Edge. 它可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。 新的 IoT 擴充功能會以裝置管理和完整 IoT Edge 功能等功能來擴充 Azure CLI 的功能。

### <a name="prerequisites-for-the-azure-cli"></a>Prerequisites for the Azure CLI

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 您環境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 您的 Azure CLI 版本至少必須是 2.0.24 或更新版本。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Create an IoT Edge device with the Azure CLI

Use the [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) command to create a new device identity in your IoT hub. 例如：

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

這個命令包含三個參數：

* **device-id**：提供 IoT 中樞內獨一無二的描述性名稱。
* **hub-name**：提供 IoT 中樞的名稱。
* **edge-enabled**：這個參數會宣告裝置是要 IoT Edge 搭配使用。

   ![az iot hub device-identity create 輸出](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>View IoT Edge devices with the Azure CLI

Use the [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) command to view all devices in your IoT hub. 例如：

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

任何註冊為 IoT Edge 裝置的裝置，其 **capabilities.iotEdge** 屬性都會設定成 **true**。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Retrieve the connection string with the Azure CLI

您準備好開始設定裝置時，需要一個利用在 IoT 中樞中的身分識別連結實體裝置的連接字串。 Use the [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) command to return the connection string for a single device:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` 參數的值區分大小寫。 請勿複製連接字串兩旁的引號。

## <a name="next-steps"></a>後續步驟

Now that you have a device identity registered in your IoT hub, you're ready to install the IoT Edge runtime on your devices. Install the runtime according to the device's operating system:

* [Install Azure IoT Edge on Windows](how-to-install-iot-edge-windows.md)
* [Install the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)
