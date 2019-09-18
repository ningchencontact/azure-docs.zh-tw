---
title: 將 IoT 隨插即用預覽版的範例裝置程式碼連線至 IoT 中樞 | Microsoft Docs
description: 建置並執行連線至 IoT 中樞的 IoT 隨插即用預覽版範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4802c120f4722796fcad4ea99f25adad368451fd
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806531"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>快速入門：將範例 IoT 隨插即用預覽版裝置應用程式連線至 IoT 中樞

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 檔案總管工具來檢視它傳送至中樞的資訊。 範例應用程式以 C 撰寫，並且包含在適用於 C 的 Azure IoT 裝置 SDK 中。解決方案開發人員可使用 Azure IoT 檔案總管工具直接了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您必須在本機電腦上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) - 在安裝 Visual Studio 時，請確實包含 **NuGet 套件管理員**元件和**使用 C++ 的桌面開發**工作負載。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="install-the-azure-iot-explorer"></a>安裝 Azure IoT 檔案總管

從[最新版本](https://github.com/Azure/azure-iot-explorer/releases)頁面下載並安裝 Azure IoT 檔案總管工具。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>準備 IoT 中樞

您的 Azure 訂用帳戶中也必須要有 Azure IoT 中樞，才能完成本快速入門。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

> [!NOTE]
> 在公開預覽期間，IoT 隨插即用功能只能在**美國中部**、**歐洲北部**和**日本東部**區域中建立的 IoT 中樞上使用。

新增適用於 Azure CLI 的 Microsoft Azure IoT 延伸模組：

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

執行下列命令，在 IoT 中樞建立裝置身分識別。 請將 **YourIoTHubName** 和 **YourDevice** 預留位置取代為您的實際名稱。 如果您沒有 IoT 中樞，請依照[下列指示建立一個](../iot-hub/iot-hub-create-using-cli.md)：

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

## <a name="prepare-the-development-environment"></a>準備開發環境

### <a name="get-azure-iot-device-sdk-for-c"></a>適用於 C 的 Azure IoT 裝置 SDK

在本快速入門中，您會準備可用來複製及建立 Azure IoT C 裝置 SDK 的開發環境。

開啟命令提示字元。 執行下列命令以複製 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

預期此作業需要幾分鐘的時間才能完成。

## <a name="build-the-code"></a>建置程式碼

您所建置的應用程式會模擬連線至 IoT 中樞的裝置。 應用程式會傳送遙測資料和屬性，並接收命令。

1. 在裝置 SDK 根資料夾中建立 `cmake` 子目錄，並瀏覽至該資料夾：

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令，以建置裝置 SDK 和產生的程式碼 Stub：

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > 如果 cmake 找不到您的 C++ 編譯器，您會在執行先前的命令時收到建置錯誤。 如果發生這種情況，請嘗試在 [Visual Studio 命令提示字元](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)上執行此命令。

## <a name="run-the-device-sample"></a>執行裝置範例

將 IoT 中樞裝置連接字串傳入作為參數，以執行您的應用程式。

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

裝置應用程式會開始將資料傳送至 IoT 中樞。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 檔案總管來驗證程式碼

1. 開啟 Azure IoT 檔案總管，您會看到 [應用程式組態]  頁面。

1. 輸入您的 IoT 中樞連接字串，然後按一下 [連線]  。

1. 連線之後，您會看到 [裝置概觀] 頁面。

1. 若要新增公司存放庫，請依序選取 [設定]  、[+ 新增]  和 [在連線的裝置上]  。

1. 在 [裝置概觀] 頁面上，尋找您先前建立的裝置身分識別，並加以選取以檢視更多詳細資料。

1. 展開識別碼為 **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** 的介面，以查看 IoT 隨插即用基本項目 - 屬性、命令和遙測資料。

1. 選取 [遙測]  頁面，以檢視裝置正在傳送的遙測資料。

1. 選取 [屬性 (不可寫入)]  頁面，以檢視裝置所報告的不可寫入屬性。

1. 選取 [屬性 (可寫入)]  頁面，以檢視您可以更新的可寫入屬性。

1. 展開屬性**名稱**，以新名稱更新，然後選取 [更新可寫入屬性]  。 

1. 若要查看 [報告屬性]  資料行中顯示的新名稱，請按一下頁面頂端的 [重新整理]  按鈕。

1. 選取 [命令]  頁面，以檢視裝置支援的所有命令。

1. 展開 **blink** 命令，並設定新的閃爍時間間隔。 選取 [傳送命令]  ，以在裝置上呼叫命令。

1. 移至模擬裝置，以確認命令是否如預期執行。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線至 IoT 隨插即用預覽版裝置並與其互動](howto-develop-solution.md)
