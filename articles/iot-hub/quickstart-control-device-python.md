---
title: 從 Azure IoT 中樞控制裝置快速入門 (Python) | Microsoft Docs
description: 在此快速入門中，您會執行兩個範例 Python 應用程式。 其中一個應用程式是後端應用程式，可以從遠端控制連線到中樞的裝置。 另一個應用程式則是模擬可以從遠端控制且連線到中樞的裝置。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: b36e5d88c67a4aabf530aa8d945c17870e9c126b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892646"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>快速入門：控制連線到 IoT 中樞的裝置 (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT 中樞是一項 Azure 服務，可讓您從雲端管理您的 IoT 裝置，並將大量的裝置遙測內嵌到雲端進行儲存或處理。 在此快速入門中，您可以使用「直接方法」  來控制連線到 IoT 中樞的模擬裝置。 您可以使用直接方法，針對連線到 IoT 中樞的裝置，從遠端變更裝置的行為。

快速入門會使用兩個預先撰寫的 Python 應用程式：

* 可回應後端應用程式直接方法呼叫的模擬裝置應用程式。 為了接收直接方法呼叫，此應用程式會連線到 IoT 中樞上的特定裝置端點。

* 在模擬裝置上呼叫直接方法的後端應用程式。 為了在裝置上呼叫直接方法，此應用程式會連線到 IoT 中樞上的服務端端點。

> [!IMPORTANT]
> 在本文中，後端應用程式會使用 Python V1 服務用戶端，而裝置應用程式會使用 Python V2 裝置用戶端。 V1 服務用戶端位於 Azure IoT Python SDK GitHub 存放庫的 [v1-deprecated 分支](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated)中。 V1 服務用戶端的 Pip 套件 (azure-iothub-service-client  ) 具有嚴格的平台限定需求，包括安裝在開發機器上的 Python 版本。 這些需求會在＜**必要條件**＞一節中註明。
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

執行下列命令，將適用於 Azure CLI 的 Microsoft Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

如果您尚未這樣做，請從 https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip 下載範例 Python 專案並將 ZIP 封存檔解壓縮。

**針對 Windows**，安裝 V1 IoT 中樞服務用戶端 Pip 套件時需要下列必要條件：

* 請確定您已安裝 [Python 版本 **3.6.x**](https://www.python.org/downloads/)。

* 請確認您已安裝[適用於 Visual Studio 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)。

**針對非 Windows 平台**，請參閱 V1 SDK 文件中的 [Python Pip 套件散發表](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md#python-pip-package-distribution-table)。 請確定為您平台指定的 Python 3.x 版和任何相關聯的需求都已安裝在您的開發機器上。 安裝 Python 3.x (不是 2.7) 可啟用 V2 裝置用戶端中的非同步作業，本快速入門也會使用此作業。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-python.md)，則可以略過此步驟。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

如果您已完成先前的[快速入門：將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-python.md)，則可以略過此步驟。

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本快速入門中，您會使用 Azure Cloud Shell 來註冊模擬的裝置。

1. 在 Azure Cloud Shell 中執行下列命令，以建立裝置身分識別。

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    **MyPythonDevice**：這是您要註冊之裝置的名稱。 建議您使用 **MyPythonDevice**，如下所示。 如果您為裝置選擇不同的名稱，則也必須在本文中使用該名稱，並先在範例應用程式中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. 在 Azure Cloud Shell 中執行下列命令，以針對您剛註冊的裝置取得_裝置連接字串_：

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    記下裝置連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。

3. 您也需要_服務連接字串_，讓後端應用程式能夠連線到您的 IoT 中樞並擷取訊息。 下列命令可擷取 IoT 中樞的服務連接字串：

    **YourIoTHubName**：以您為 IoT 中樞選擇的名稱取代此預留位置。

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    記下服務連接字串，它看起來如下：

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    您稍後會在快速入門中使用此值。 服務連接字符串與您在上一個步驟中記下的裝置連接字串不同。

## <a name="listen-for-direct-method-calls"></a>接聽直接方法呼叫

模擬裝置應用程式會連線到 IoT 中樞上的特定裝置端點、傳送模擬的遙測，並接聽來自中樞的直接方法呼叫。 在此快速入門中，來自中樞的直接方法呼叫會告知裝置變更其傳送遙測的間隔。 模擬的裝置在執行直接方法後，會將通知傳送回您的中樞。

1. 在本機終端機視窗中，瀏覽至範例 Python 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device-2** 資料夾。

1. 在您選擇的文字編輯器中開啟 **SimulatedDevice.py** 檔案。

    使用您稍早所記錄的裝置連接字串來取代 `CONNECTION_STRING` 變數的值。 然後將變更儲存到 **SimulatedDevice.py** 檔案。

1. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的程式庫：

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 在本機終端機視窗中，執行下列命令以執行模擬裝置應用程式：

    ```cmd/sh
    python SimulatedDevice.py
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![執行模擬的裝置](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>呼叫直接方法

後端應用程式會連線到 IoT 中樞上的服務端端點。 應用程式會透過您的 IoT 中樞對裝置進行直接方法呼叫，並接聽通知。 IoT 中樞後端應用程式通常會在雲端中執行。

1. 在另一個本機終端機視窗中，瀏覽至範例 Python 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\back-end-application** 資料夾。

1. 在您選擇的文字編輯器中開啟 **BackEndApplication.py** 檔案。

    使用稍早所記錄的服務連接字串來取代 `CONNECTION_STRING` 變數的值。 然後將您的變更儲存到 **BackEndApplication.py**。

1. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的程式庫：

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. 在本機終端機視窗中，執行下列命令以執行後端應用程式：

    ```cmd/sh
    python BackEndApplication.py
    ```

    下列螢幕擷取畫面顯示應用程式對裝置進行直接方法呼叫並接收通知時的輸出：

    ![執行後端應用程式](./media/quickstart-control-device-python/BackEndApplication.png)

    執行後端應用程式之後，在執行模擬裝置的主控台視窗中將會出現一則訊息，且它傳送訊息的速率也會變更：

    ![模擬用戶端的變更](./media/quickstart-control-device-python/SimulatedDevice-2.png)

    > [!NOTE]
    > 如果您在匯入 iothub_service_client  時收到錯誤，請確定您已安裝[必要條件](#prerequisites)中針對平台指定的正確 Python 版本，以及任何其他相關成品。 如果在驗證必要條件之後，您仍然收到錯誤，您可能需要為平台建立服務用戶端。 若要了解如何為您的平台建立 SDK，請參閱 V1 SDK 文件中的 [devbox 安裝指示](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md)。
    >

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您從後端應用程式對裝置進行直接方法呼叫，並在模擬裝置應用程式中回應直接方法呼叫。

若要了解如何將「裝置到雲端」訊息路由傳送至雲端中的不同目的地，請繼續下一個教學課程。

> [!div class="nextstepaction"]
> [教學課程：將遙測路由傳送至不同的端點以進行處理](tutorial-routing.md)