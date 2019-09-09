---
title: 將 IoT 隨插即用裝置連線到 IoT Central |Microsoft Docs
description: 身為裝置開發人員，請瞭解如何使用 Visual Studio Code 來建立及測試連接至 IoT Central 的 IoT 隨插即用裝置。
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997235"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>使用 Visual Studio Code 建立連線至的 IoT 隨插即用裝置 IoT Central

本操作指南說明如何：

* 身為操作員，可在 Azure IoT Central 應用程式中新增和設定實際裝置。

* 身為裝置開發人員，使用 Visual Studio Code 建立連線至 IoT Central 應用程式的[IoT 隨插即用](../iot-pnp/overview-iot-plug-and-play.md)裝置。

您可以使用[裝置功能模型](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)來定義連接到 IoT Central 的裝置。 在本指南中，您會使用定義環境感應器裝置的模型。

裝置開發人員會使用模型來產生裝置用戶端程式代碼，而建立器會使用模型在 IoT Central 中[建立裝置範本](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。 此模型會作為您的裝置與 IoT Central 應用程式之間的合約。

本指南中的一節描述如何建立所產生的程式碼，假設您使用的是 Windows。

在本指南中，您將了解如何：

* 將裝置功能模型匯入 IoT Central 中的裝置範本
* 將儀表板新增至顯示裝置遙測的範本
* 從範本新增實際裝置
* 將裝置功能模型匯入 Visual Studio Code
* 從模型產生 C 裝置用戶端應用程式
* 建立 C 裝置用戶端應用程式，並將它連接到 IoT Central

## <a name="prerequisites"></a>必要條件

若要測試您在本指南中的裝置程式碼，您需要從**預覽**應用程式範本建立的 IoT Central 應用程式。 如果您還沒有要使用的 IoT Central 應用程式，請完成快速入門[建立 Azure IoT Central 應用程式（預覽功能）](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)：

若要使用本指南中的裝置功能模型，您需要：

* [Visual Studio Code](https://code.visualstudio.com/download)：Visual Studio Code 適用于多個平臺
* 適用于 Visual Studio Code 的 Azure IoT 裝置工作臺延伸模組。 請使用下列步驟，在 VS Code 中安裝 Azure IoT Device Workbench 延伸模組：

    1. 在 VS Code 中，選取 [延伸模組] 索引標籤。
    1. 搜尋 **Azure IoT Device Workbench**。
    1. 選取 [安裝]。

    > [!NOTE]
    > 延伸模組中目前的程式碼產生器版本不支援**Geopoint**和**向量**架構類型，或**加速**、**速度**和**位置**的語義類型。 IoT Central 支援這些架構和語義類型。

    > [!NOTE]
    > 若要使用 IoT Central，裝置功能模型必須具有在相同檔案中以內嵌方式定義的所有介面。

若要在本指南的 Windows 上建立產生的 C 程式碼，您需要：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) - 在安裝 Visual Studio 時，請確實包含 **NuGet 套件管理員**元件和**使用 C++ 的桌面開發**工作負載。
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/) - 當您安裝 **CMake** 時，請選取 [將 CMake 新增至系統路徑] 選項。
* Azure IoT C SDK 的本機複本：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

若要遵循本操作說明結尾的裝置優先指示，您也必須安裝：

* [Node.js](https://nodejs.org)
* [Dps-keygen 工具](https://www.npmjs.com/package/dps-keygen)：

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>建立裝置範本

在包含您所複製 Azure IoT C SDK 的**azure iot-sdk-c**資料夾中，建立名為**pnp_app**的資料夾。 從 GitHub 下載[EnvironmentalSensor. capabilitymodel](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)裝置功能模型，並將檔案儲存在**pnp_app**資料夾中。 `<YOUR_COMPANY_NAME_HERE>`以您的名稱取代的兩個實例。 您只能使用字元 a-z、A-Z、0-9 和底線。 記下`"@id"`欄位的完整值，以唯一識別您的裝置功能模型，您稍後需要用到它。 此模型包含兩個介面：

* 通用介面。
* **EnvironmentalSensor**自訂介面。

若要在 IoT Central 中建立環境感應器裝置的裝置範本：

1. 移至 [**裝置範本**] 頁面，然後選取 [ **+ 新增**]。 然後選擇 [**自訂**]。

1. 輸入**環境感應器**作為裝置範本名稱。

1. 選擇 [匯**入功能模型**]。 然後尋找您建立的**EnvironmentalSensor capabilitymodel** ，然後選取 [**開啟**]。 這兩個介面、**裝置資訊**和**環境感應器**都會顯示在**環境感應器功能模型**中。

1. 選取 [ **Views** ]，然後**將裝置視覺化**。

1. 在您可以新增至儀表板的欄位清單中，選取兩個**遙測**值 [**濕度**] 和 [**溫度**]，然後選取 [**合併**]。 然後，選擇 [儲存]。

您現在有一個使用**環境感應器**型號的裝置範本，它有一個簡單的儀表板，可顯示裝置的遙測。

## <a name="publish-the-template-and-add-a-real-device"></a>發佈範本並新增實際裝置

若要發佈範本並新增實際裝置，請移至 [**裝置範本**] 頁面，然後選取 [**環境感應器**] 裝置範本。 選取 [**發行**]，檢查資訊，然後選取 [**發佈**]。

連接用戶端應用程式之前，您必須先在 [**裝置**] 頁面上新增裝置，並取得其連接資訊：

1. 移至 [**裝置**] 頁面，然後選取 [**環境感應器**]。 [**裝置**] 頁面可讓您管理可以連線到應用程式的裝置。

1. 若要新增實際裝置，請選擇 [ **+ 新增**]，將 [裝置識別碼] 變更為 [ **sensor01**]，然後選取 [**建立**]。 請確定**模擬**已**關閉**。

1. 在裝置清單中，選取您的**環境感應器**裝置。 然後選取 **[連線]** 。

1. 記下 [**範圍識別碼**]、[**裝置識別碼**] 和 [**主要金鑰**]。 然後選取 [**關閉**]。

## <a name="generate-a-device-client-application"></a>產生裝置用戶端應用程式

您可以使用 Visual Studio Code 從裝置功能模型產生基本架構裝置用戶端應用程式：

1. 啟動 Visual Studio Code 並開啟 [ **pnp_app** ] 資料夾。

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**然後選取 [**產生裝置程式碼 Stub**]。

1. 選取 [ **EnvironmentalSensor capabilitymodel** ] 裝置功能模型檔案。

1. 輸入**sensor_app**做為專案名稱。

1. 選擇 [ANSI C] 作為語言。

1. 選擇 [CMake 專案] 作為目標。

1. 選擇 [透過 DPS (裝置佈建服務) 對稱金鑰] 作為連線方法。

Visual Studio Code 會開啟新視窗，其中包含在**sensor_app**資料夾中產生的 C 程式碼。

## <a name="add-connection-details-to-the-device-client"></a>將連線詳細資料新增至裝置用戶端

若要將連接資訊新增至裝置用戶端，請在包含所產生程式碼的資料夾中開啟**main. c** ：

1. 將`[DPS Id Scope]`取代為您先前所記下的**範圍識別碼**值。

1. 將`[DPS symmetric key]`取代為您先前所記下的**主要金鑰**值。

1. 將`[device registration Id]`取代為您先前所記下的**裝置識別碼**值。

1. 儲存變更。

## <a name="build-and-run-the-client"></a>建立並執行用戶端

若要建立並執行用戶端，您需要自訂 Azure IoT C SDK 的組建：

1. 在**remote monitoring.h cmakelists.txt**的根目錄中，開啟**檔案 [檔案**]。

1. 在此檔案的結尾新增下列這一行，以在組建中包含新的用戶端應用程式：

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. 儲存變更。

1. 開啟命令提示字元，然後流覽至 [ **azure-iot-sdk-c** ] 資料夾。

1. 若要建立應用程式，請執行下列命令：

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. 若要執行應用程式，請從相同的命令提示字元執行下列命令：

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>檢視遙測資料

幾分鐘後，您就可以在 IoT Central 應用程式的裝置儀表板上看到來自裝置的遙測。

## <a name="connect-device-first"></a>連接裝置-第一

如連線中所述，您可以透過裝置優先連線來連接 IoT 隨插即用[裝置。](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 探索程式會遵循此順序：

1. 與裝置範本相關聯（如果已在 IoT Central 應用程式中發佈）。

1. 從已發行和已認證功能模型的[公用存放庫](https://aka.ms/ACFI)提取功能模型。

使用 Visual Studio Code 和上述的 [**產生裝置程式碼 Stub** ] 命令，您可以遵循下列步驟，將裝置優先連線並自動從公用存放庫將已發佈的裝置功能模型帶入 IoT Central：

1. 使用已在公用儲存機制中發行的現有裝置功能模型。 您需要完整的裝置功能模型，並記下此模型的 URN。

1. 請遵循上述步驟來[產生裝置用戶端應用程式](#generate-a-device-client-application)，以使用 Visual Studio Code 並產生裝置程式碼。

1. 從您的 IoT Central 應用程式中，移至 [系統**管理**] 索引標籤，然後選取 [**裝置**連線] 區段。 記下應用程式的**範圍識別碼**和**主要金鑰**值。

    > [!NOTE]
    > 此頁面上顯示的**主要金鑰**是群組共用存取簽章，可用來為您的應用程式產生多個裝置金鑰。

1. 使用[DPS keygen](https://www.npmjs.com/package/dps-keygen)工具，從您先前所記下的主要金鑰產生裝置金鑰。 若要產生裝置金鑰，請執行下列命令：

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. 依照上一節中的步驟，[將連線詳細資料新增至裝置用戶端](#add-connection-details-to-the-device-client)，以新增**範圍識別碼**、**主要金鑰**和**裝置識別碼**。

1. 請遵循上一節中的步驟來[建立和執行用戶端](#build-and-run-the-client)。

1. 現在您會看到裝置連接到您的 IoT Central 應用程式，並自動將公用存放庫中的裝置功能模型帶入裝置範本。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何將真實裝置連線到 IoT Central，建議的下一個步驟是深入瞭解[設定裝置範本](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)中的裝置範本
