---
title: IoT DevKit 到雲端 -- 將 IoT DevKit AZ3166 連線到 Azure IoT 中樞 | Microsoft Docs
description: 在本教學課程中了解如何設定 IoT DevKit AZ3166 並將其連線到 Azure IoT 中樞，以便它可以將資料傳送到 Azure 雲端平台。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/17/2019
ms.author: wesmc
ms.openlocfilehash: 2f86b74299b5d47a87ed0b8e89a992f0f91a84be
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924637"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>将 IoT DevKit AZ3166 连接到 Azure IoT 中心

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

您可以使用 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 來開發及設定運用 Microsoft Azure 服務的物聯網 (IoT) 解決方案原型。 它包含的 Arduino 相容板，具有豐富週邊設備和感應器、 開放原始碼開發板套件和豐富[範例庫](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-learn"></a>您學到什麼

* 如何建立 IoT 中樞並註冊 MXChip IoT DevKit 適用的裝置。
* 如何將 IoT DevKit 連線至 Wi-fi 和設定的 IoT 中樞連接字串。
* 如何將 DevKit 感應器的遙測資料傳送至 IoT 中樞。
* 如何準備開發環境和開發 IoT devkit 適用的應用程式。

還沒有 DevKit 嗎？ 請試用 [DevKit 模擬器](https://azure-samples.github.io/iot-devkit-web-simulator/)或[購買 DevKit](https://aka.ms/iot-devkit-purchase)。

## <a name="what-you-need"></a>您需要什麼

* 使用 Micro USB 纜線的 MXChip IoT DevKit 面板。 [立即買一個](https://aka.ms/iot-devkit-purchase)。
* 執行 Windows 10，macOS 10.10 + 或 Ubuntu 18.04 + 的電腦。
* 有效的 Azure 訂用帳戶。 [激活 30 天免费试用版 Microsoft Azure 帐户](https://azureinfo.microsoft.com/us-freetrial.html)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>准备硬件

将以下硬件挂接到计算机：

* DevKit 板
* Micro-USB 电缆

![所需硬件](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

请按照以下步骤将 DevKit 连接到计算机：

1. 将 USB 端连接到计算机。

2. 將 Micro-USB 端連線到 DevKit。

3. 綠色的電源 LED 燈可確認連線成功。

   ![硬體連線](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>快速入門：將遙測從 DevKit 傳送到 IoT 中樞

快速入門會使用預先編譯的 DevKit 韌體將遙測傳送至 IoT 中樞。 您在執行之前，您會建立 IoT 中樞，並向中樞註冊裝置。

### <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

   **MyNodeDevice**：您所註冊的裝置名稱。 使用所示的 **MyNodeDevice**。 如果您為裝置選擇不同的名稱，則必須在本文中使用該名稱，並先在應用程式範例中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```
   > [!NOTE]
   > 如果您執行時發生`device-identity`，安裝[Azure CLI 的 Azure IOT 擴充功能](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md)如需詳細資訊。
  
1. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

   **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

### <a name="send-devkit-telemetry"></a>將 DevKit 遙測資料傳送

將 DevKit 連線至您的 IoT 中樞的裝置特定端點，並將傳送溫度和溼度遙測。

1. 下載最新版[GetStarted 韌體](https://aka.ms/devkit/prod/getstarted/latest)IoT devkit 適用。

1. 請確定 IoT devkit 連線到您的電腦透過 USB 連線。 開啟檔案總管 中沒有呼叫 USB 大型存放裝置**AZ3166**。
    ![開啟 Windows 檔案總管](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. 拖放到大型存放裝置，且該剛才下載的韌體會自動閃爍。
    ![複製韌體](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. 在 DevKit，按住按鈕**B**、 按下再放開**重設** 按鈕，然後放開按鈕**B**。您的 DevKit 會進入 AP 模式。 若要確認，畫面會顯示 DevKit 和設定入口網站的 IP 位址的服務組識別元 (SSID)。
    ![重設 按鈕、 按鈕 B 和 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![設定 AP 模式](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 使用網頁瀏覽器在不同的 Wi-fi 上已啟用裝置 （電腦或行動電話） 連接到 IoT DevKit SSID，顯示在上一個步驟。 如果它要求輸入密碼，請將它保留空白。
    ![連接 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. 開啟**192.168.0.1**瀏覽器中。 選取您想要將 IoT DevKit 連線至、 輸入 Wi-fi 密碼，然後貼上您所做的先前記下的裝置連接字串的 Wi-fi。 然後按一下 [儲存]。
    ![設定 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit 僅支援 2.4 GHz 網路。 如需更多詳細資料，請參閱[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)。

1. 將 IoT devkit 連線至儲存 WiFi 資訊和裝置連接字串，當您看到 [結果] 頁面。
    ![組態結果](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > 設定好 Wi-Fi 後，即使將裝置的接線拔除，該裝置上仍會留有用於該連線的認證。

1. IoT DevKit 會重新啟動在幾秒鐘的時間。 在 DevKit 畫面上，您會看到 DevKit 的 IP 位址會依循遙測資料，包括溫度和溼度訊息計數的值傳送至 Azure IoT 中樞。
    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![傳送資料](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

## <a name="prepare-the-development-environment"></a>準備開發環境

請遵循下列步驟來準備 DevKit 開發環境：

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>使用 Azure IoT 工具延伸模組套件安裝 Visual Studio Code

1. 安裝 [Arduino IDE](https://www.arduino.cc/en/Main/Software)。 此 IDE 提供必要的工具链用于编译和上传 Arduino 代码。
    * Windows：使用 Windows Installer 版本。 請勿從 App Store 安裝。
    * **macOS**：將解壓縮的 **Arduino.app** 拖放到 `/Applications` 資料夾中。
    * **Ubuntu**：將它解壓縮到資料夾中，例如 `$HOME/Downloads/arduino-1.8.8`

2. 安裝[Visual Studio Code](https://code.visualstudio.com/)、 跨平台的原始程式碼編輯器具有功能強大的 intellisense、 程式碼完成和偵錯支援，以及豐富的擴充功能可以從 marketplace 進行安裝。

3. 啟動 VS Code，在延伸模組市集中尋找 **Arduino** 並加以安裝。 此延伸模組提供在 Arduino 平台上進行開發的增強體驗。
    ![安裝 Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 在延伸模組市集中尋找 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 並加以安裝。
    ![安裝 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    或者，使用下列直接連結：
    > [!div class="nextstepaction"]
    > [安裝 Azure IoT 工具延伸模組套件](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Azure IoT 工具延伸模組組件包含[Azure IoT 裝置 Workbench](https://aka.ms/iot-workbench)用來開發及偵錯各種 IoT devkit 裝置上。 [Azure IoT 中樞 Toolkit](https://aka.ms/iot-toolkit)也隨附的 Azure IoT 工具延伸模組組件，用來管理，以及與 Azure IoT 中樞互動。

5. 為 VS Code 設定 Arduino 設定。

    在 Visual Studio Code 中，单击“文件”>“首选项”>“设置”。 依次单击 **...** 和“打开 settings.json”。
    ![安裝 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    根据你的平台添加以下行来配置 Arduino： 

    * Windows：
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**：

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**：
    
        以您的使用者名稱取代下方的 **{username}** 預留位置。

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Arduino:Board Manager]。 搜索 **AZ3166** 并安装最新版本。
    ![安裝 DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>安裝 ST-Link 驅動程式

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) 是 IoT DevKit 用来与开发计算机通信的 USB 接口。 需要将它安装在 Windows 上才能将编译的设备代码刷写到 DevKit。 遵循 OS 特定的步骤，使计算机能够访问你的设备。

* **Windows**：下載並安裝 USB 驅動程式，從[STMicroelectronics 網站](https://www.st.com/en/development-tools/stsw-link009.html)或是[直接連結](https://aka.ms/stlink-v2-windows)。
* **macOS**：macOS 不需要任何驅動程式。
* **Ubuntu**：在终端中运行以下命令，然后注销并登录，使组更改生效：
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

您現在已可準備及設定您的開發環境。 讓我們建置您剛剛執行 GetStarted 範例。

## <a name="build-your-first-project"></a>建立您的第一個專案

### <a name="open-sample-code-from-sample-gallery"></a>从示例库打开示例代码

IoT DevKit 包含豐富的資源庫的範例，您可以用來了解將 DevKit 連線至各種 Azure 服務。

1. 確定 IoT DevKit **未**連線至您的電腦。 先启动 VS Code，然后将 DevKit 连接到计算机。

1. 按一下 `F1` 以開啟命令選擇區，輸入並選取 [Azure IoT Device Workbench：**開啟範例...]**。然后选择“IoT DevKit”作为开发板。

1. 在 IoT Workbench 的“示例”页中，找到“入门”并单击“打开示例”。 然後選取預設路徑來下載範例程式碼。
    ![開啟範例](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>预配 Azure IoT 中心和设备

不需要佈建的 Azure IoT 中樞和 Azure 入口網站的裝置，您可以進行在 VS Code 中而不需要離開的開發環境。

1. 在新開啟的專案視窗中，按一下 `F1` 以開啟命令選擇區，鍵入並選取 [Azure IoT Device Workbench:Provision Azure Services...] \(Azure IoT Device Workbench：佈建 Azure 服務...\)。遵照分步指南完成预配 Azure IoT 中心和创建 IoT 中心设备的操作。
    ![预配命令](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > 如果你尚未登录到 Azure， 请遵循弹出的通知登录。

1. 选择要使用的订阅。
    ![选择订阅](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. 然后选择或新建一个[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)。
    ![选择资源组](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 在指定的资源组中，遵循指南选择或新建 Azure IoT 中心。
    ![选择 IoT 中心的步骤](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![选择 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![选定的 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 在输出窗口中，可以看到预配的 Azure IoT 中心![预配的 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. 在预配的 Azure IoT 中心内选择或新建一个设备。
    ![选择 IoT 设备的步骤](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![选择预配的 IoT 设备](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. 现已预配 Azure IoT 中心并在其中创建了设备。 另外，设备连接字符串将保存在 VS Code 中，供将来配置 IoT DevKit。
    ![完成预配](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>配置并编译设备代码

1. 在右下方的状态栏中，检查选定的开发板是否为 **MXCHIP AZ3166**，以及是否使用了带有 **STMicroelectronics** 的串行端口。
    ![选择开发板和 COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. 按一下 `F1` 以開啟命令選擇區，鍵入並選取 [Azure IoT Device Workbench:Configure Device Settings...] \(Azure IoT Device Workbench：設定裝置設定...\)，然後選取 [Config Device Connection String] \(設定裝置連接字串\) > [Select IoT Hub Device Connection String] \(選取 IoT 中樞裝置連接字串\)。

1. 在 DevKit 上，按住**按钮 A** 不放，按下再松开**重置**按钮，然后松开**按钮 A**。您的 DevKit 會進入設定模式並儲存連接字串。
    ![連接字串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. 再按一次 `F1`，鍵入並選取 [Azure IoT Device Workbench:**上傳裝置程式碼]**。 即會開始編譯程式碼，並上傳到 DevKit。
    ![Arduino 上傳](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit 會重新開機，然後開始執行程式碼。

> [!NOTE]
> 如果發生任何錯誤或中斷，您一律可藉由再次執行命令來復原。

## <a name="test-the-project"></a>测试项目

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>查看发送到 Azure IoT 中心的遥测数据

按一下狀態列上的電源插頭圖示，以開啟「序列監視器」：![序列監視器](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

当看到以下结果时，示例应用程序已成功运行：

* 串行监视器显示发送到 IoT 中心的消息。
* MXChip IoT DevKit 上的 LED 灯闪烁。

![串行监视器输出](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>查看 Azure IoT 中心收到的遥测数据

您可以使用 [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 來監視 IoT 中樞的裝置到雲端 (D2C) 訊息。

1. 登录 [Azure 门户](https://portal.azure.com/)，并找到创建的 IoT 中心。
    ![Azure 门户](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. 在“共享访问策略”窗格中单击“iothubowner 策略”，并记下 IoT 中心的连接字符串。
    ![Azure IoT 中心连接字符串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. 在 VS Code 中，按一下 `F1`，鍵入並選取 [Azure IoT 中樞：設定 IoT 中樞連接字串]。 將連接字串複製到其中。
    ![设置 Azure IoT 中心连接字符串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 展開右側的 [AZURE IOT 中樞裝置] 窗格，以滑鼠右鍵按一下您所建立的裝置名稱，然後選取 [開始監視 D2C 訊息]。
    ![監視 D2C 訊息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. 在“输出”窗格中，可以看到传入 IoT 中心的 D2C 消息。
    ![D2C 消息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>查看代码

`GetStarted.ino` 是主要的 Arduino 草图文件。

![D2C 消息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

若要查看设备遥测数据如何发送到 Azure IoT 中心，请打开同一文件夹中的 `utility.cpp` 文件。 查看 [API 参考](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/)，了解如何在 IoT DevKit 中使用传感器和外围设备。

使用的 `DevKitMQTTClient` 是[适用于 C 的 Microsoft Azure IoT SDK 和库](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client)中 **iothub_client** 的包装器，用于与 Azure IoT 中心交互。

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，可在 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)中查看解决方法，或者通过 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) 联系我们。 还可在此页中留言，向我们提供反馈。

## <a name="next-steps"></a>后续步骤

现已成功将 MXChip IoT DevKit 连接到 IoT 中心，并将捕获的传感器数据发送到了 IoT 中心。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
