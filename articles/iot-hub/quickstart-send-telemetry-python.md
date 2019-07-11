---
title: 將遙測傳送至 Azure IoT 中樞快速入門 (Python) | Microsoft Docs
description: 本快速入門中，您可以執行範例 Python 應用程式，將模擬的遙測資料傳送到 IoT 中樞，並使用公用程式從 IoT 中樞讀取遙測資料。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/28/2019
ms.openlocfilehash: a8abd71609d3e063c92541485007a3bde44be954
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051247"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>快速入門：將遙測從裝置傳送至 IoT 中樞，並使用後端應用程式讀取遙測 (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT 中樞是一項 Azure 服務，可讓您從 IoT 裝置將大量的遙測擷取到雲端進行儲存或處理。 在此快速入門中，您透過 IoT 中樞，將遙測從模擬裝置應用程式傳送到後端應用程式以進行處理。

本快速入門會使用預先撰寫的 Python 應用程式傳送遙測，以及使用 CLI 公用程式從中樞讀取遙測。 在執行下列兩個應用程式之前，您需要建立一個 IoT 中樞，並向中樞註冊一個裝置。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

您在此快速入門中執行的應用程式範例是使用 Python 所撰寫的。 目前，適用於 Python 的 Microsoft Azure IoT SDK 只對每個平台支援特定版本的 Python。 若要深入了解，請參閱 [Python SDK 讀我檔案](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)。

本快速入門假設您使用 Windows 開發電腦。 對於 Windows 系統，僅支援 [Python 3.6.x](https://www.python.org/downloads/release/python-368/)。 您選擇的 Python 安裝程式應該要以您正在使用的系統架構為基礎。 如果您的系統 CPU 架構是 32 位元，則下載 x86 安裝程式；若是 64 位元架構，則下載 x86-64 安裝程式。 此外，請確定[適用於 Visual Studio 2019 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)已針對您的架構 (x86 或 x64) 安裝。

您可以從 [Python.org](https://www.python.org/downloads/) 下載適用於其他平台的 Python。

您可以使用下列命令之一，以確認開發電腦上目前的 Python 版本：

```python
python --version
```

```python
python3 --version
```

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

從 https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip 下載範例 Python 專案並將 ZIP 封存檔解壓縮。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    **MyPythonDevice**：這是為已註冊裝置指定的名稱。 使用所示的 MyPythonDevice。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. 在 Azure Cloud Shell 中執行下列命令，以取得您所註冊裝置的「裝置連接字串」  ：

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

## <a name="send-simulated-telemetry"></a>傳送模擬的遙測

模擬裝置應用程式會連線到 IoT 中樞上的裝置特定端點，並且傳送模擬的溫度和溼度遙測。

1. 在本機終端機視窗中，瀏覽至範例 Python 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device** 資料夾。

1. 在您選擇的文字編輯器中開啟 **SimulatedDevice.py** 檔案。

    使用先前所記錄的裝置連接字串來取代 `CONNECTION_STRING` 變數的值。 然後將變更儲存到 **SimulatedDevice.py** 檔案。

1. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的程式庫：

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 在本機終端機視窗中，執行下列命令以執行模擬裝置應用程式：

    ```cmd/sh
    python SimulatedDevice.py
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![執行模擬的裝置](media/quickstart-send-telemetry-python/SimulatedDevice.png)
    
### <a name="to-avoid-the-import-iothubclient-error"></a>若要避免匯入 iothub_client 錯誤
目前的 Azure IoT SDK for Python 版本是[我們的 C SDK](https://github.com/azure/azure-iot-sdk-c) 的包裝函式。 該包裝函式是使用 [Boost](https://www.boost.org/) 程式庫來產生。 因而附帶了一些重要的限制。 如需詳細資訊，請參閱[這裡](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)。

1. 檢查您是否有正確的 [Python](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues) 版本。 請注意，只有特定版本適用於此範例。 
2. 確認您有正確的 C++執行階段版本：[適用於 Visual Studio 2019 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)。 (我們建議使用最新版本)。
3. 確認您已安裝 iothub 用戶端：`pip install azure-iothub-device-client`。

## <a name="read-the-telemetry-from-your-hub"></a>從您的中樞讀取遙測

IoT 中樞 CLI 擴充功能可以連線到 IoT 中樞上的服務端**事件**端點。 擴充功能會接收模擬裝置所傳送的「裝置到雲端」訊息。 IoT 中樞後端應用程式通常在雲端中執行，以接收和處理「裝置到雲端」訊息。

在 Azure Cloud Shell 中執行下列命令，以您的 IoT 中樞名稱取代 `YourIoTHubName`：

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

下列螢幕擷取畫面顯示由模擬裝置傳送遙測至中樞時，擴充功能接收遙測的輸出：

![執行後端應用程式](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已經設定 IoT 中樞、註冊裝置、使用 Python 應用程式將模擬的遙測傳送到中樞，並使用簡單的後端應用程式從中樞讀取遙測。

若要了解如何從後端應用程式控制您的模擬裝置，請繼續下一個快速入門。

> [!div class="nextstepaction"]
> [快速入門：控制連線到 IoT 中樞的裝置](quickstart-control-device-python.md)
