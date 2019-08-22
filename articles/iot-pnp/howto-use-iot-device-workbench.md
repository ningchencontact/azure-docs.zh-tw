---
title: 使用 Azure IoT 裝置工作臺來建立 IoT 隨插即用預覽裝置 |Microsoft Docs
description: 在 Visual Studio Code 中使用 Azure IoT 裝置工作臺延伸模組, 以加速撰寫 IoT 隨插即用裝置型號和執行裝置程式碼。
author: liydu
ms.author: liydu
ms.date: 07/25/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: eaf1e313e3f88e151576ff00aec762a5fc2d8c66
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880484"
---
# <a name="use-azure-iot-device-workbench-extension-in-visual-studio-code"></a>在 Visual Studio Code 中使用 Azure IoT 裝置工作臺擴充功能

Azure IoT 裝置工作臺 Visual Studio Code 延伸模組提供整合式環境, 可撰寫裝置功能模型 (DCM) 和介面、發行至模型存放庫, 以及產生基本架構 C 程式碼來執行裝置應用程式。

本文將說明如何：

- 產生裝置程式碼和應用程式專案。
- 在您的裝置專案中使用產生的程式碼。
- 重新產生基本架構程式碼來進行反復執行。

若要深入瞭解如何使用裝置工作臺延伸模組來開發 IoT 裝置, [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)請參閱。

## <a name="prerequisites"></a>必要條件

安裝 [Visual Studio Code](https://code.visualstudio.com/)。

請使用下列步驟在 VS Code 中安裝延伸模組。

1. 在 VS Code 中, 選取 [**擴充**功能] 索引標籤。
1. 從 marketplace 搜尋並安裝**Azure IoT 裝置工作臺**。

## <a name="generate-device-code-and-project"></a>產生裝置程式碼和專案

在 VS Code 中, 使用**Ctrl + Shift + P**開啟命令選擇區, 輸入**IoT 隨插即用**, 然後選取 [**產生裝置程式碼 Stub** ] 來設定基本架構程式碼和專案類型。 下列清單詳細說明每個步驟:

- **要用於產生程式碼的 DCM**檔案。 若要產生基本架構裝置程式碼, 請開啟包含它所執行之 DCM 和介面檔案的資料夾。 如果程式碼產生器找不到 DCM 所需的介面, 它會視需要從模型存放庫下載它。

- **裝置程式碼語言**。 目前, 程式碼產生器僅支援 ANSI C。

- **專案名稱**。 專案名稱是用來做為所產生程式碼和其他專案檔的資料夾名稱。 根據預設, 資料夾會放在 DCM 檔案的旁邊。 若要避免在每次更新您的 DCM 並重新產生裝置程式碼時, 手動複製所產生的程式碼資料夾, 請將您的 DCM 檔案保存在與專案資料夾相同的資料夾中。

- **專案類型**。 程式碼產生器也會產生專案檔, 讓您可以將程式碼整合到您自己的專案或裝置 SDK 專案。 目前支援的專案類型為:

    - **CMake 專案**: 適用于使用[CMake](https://cmake.org/)做為組建系統的裝置專案。 此選項會在`CMakeLists.txt`與 C 程式碼相同的資料夾中產生檔案。
    - **MXChip Iot DevKit 專案**: 適用于在[MXChip IoT DevKit](https://aka.ms/iot-devkit)裝置上執行的裝置專案。 此選項會產生 Arduino 專案, 您可以[在 VS Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)或在 Arduino IDE 中用來在 IoT DevKit 裝置上建立和執行。

- **連接到 Azure IoT 的方法**。 產生的檔案也包含程式碼, 可將裝置設定為連接到 Azure IoT 中樞。 您可以選擇直接連接到[Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)或使用裝置布建[服務](https://docs.microsoft.com/azure/iot-dps)。

    - 透過**IoT 中樞裝置連接字串**: 指定裝置應用程式要直接連接到 IoT 中樞的裝置連接字串。
    - 透過**dps 對稱金鑰**: 指定要使用 DPS 連線到 IoT 中樞或 IoT Central 所需之裝置應用程式的**範圍識別碼**、**註冊識別碼**和**SaS 金鑰**。

程式碼產生器會嘗試使用位於本機資料夾中的 DCM 和介面檔案。 如果介面檔案不在本機資料夾中, 程式碼產生器會在公用模型存放庫或公司模型存放庫中尋找這些檔案。 [通用介面](./concepts-common-interfaces.md)檔案會儲存在公用模型存放庫中。

程式碼產生完成之後, 延伸模組會使用程式碼開啟新的 VS Code 視窗。 如果您開啟產生的檔案 (如**main. c**), 您可能會發現 IntelliSense 回報它無法開啟 c SDK 原始程式檔。 若要啟用正確的 IntelliSense 和程式碼導覽, 請使用下列步驟來包含 C SDK 來源:

1. 在 VS Code 中, 使用**Ctrl + Shift + P**開啟命令選擇區, 輸入並選取**C/C++:編輯設定 (JSON)** 以開啟**c_cpp_properties。**

1. 在`includePath`區段中新增裝置 SDK 的路徑:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. 儲存檔案。

## <a name="use-the-generated-code"></a>使用產生的程式碼

下列指示說明如何在不同的開發電腦平臺上, 使用您自己的裝置專案中產生的程式碼。 指示會假設您使用 IoT 中樞的裝置連接字串搭配產生的程式碼:

### <a name="linux"></a>Linux

若要使用 CMake 在 Linux 環境 (例如 Ubuntu 或 Debian) 中搭配裝置 C SDK 來建立裝置程式碼:

1. 開啟終端機應用程式。

1. `cmake`使用命令`apt-get`來安裝**GCC**、 **Git**、和所有相依性:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    確認的`cmake`版本高於**2.8.12** , 且**GCC**的版本高於**4.4.7**。

    ```sh
    cmake --version
    gcc --version
    ```

1. 複製[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)存放庫:

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    預期此作業需要幾分鐘的時間才能完成。

1. 將包含所產生程式碼的資料夾複製到裝置 SDK 根資料夾。

1. 在 VS Code 中, 開啟`CMakeLists.txt`裝置 SDK 根資料夾中的檔案。

1. 在`CMakeLists.txt`檔案結尾新增下列這一行, 以在您編譯 SDK 時包含裝置程式碼 stub 資料夾:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. 在裝置 SDK 根`cmake`資料夾中建立名為的資料夾, 然後流覽至該資料夾。

    ```sh
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令, 以使用 CMake 來建立裝置 SDK 和產生的程式程式碼端:

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .

1. After the build succeeds, run the application specifying the IoT Hub device connection string as parameter.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

若要使用 Visual StudioC++ CMake 與 Windows 上的裝置 c SDK 建立裝置程式碼, 請在命令列中, 參閱[IoT 隨插即用快速入門](./quickstart-create-pnp-device.md)。 下列步驟示範如何在 Visual Studio 中, 將裝置程式碼與裝置 C SDK 一起建立為 CMake 專案。

1. 安裝[Visual Studio 2019 (「社區」、「專業」或「企業」)](https://visualstudio.microsoft.com/downloads/) -請確定您包含**NuGet 套件管理員**元件和使用**C++** 工作負載的桌面開發。

1. 開啟 Visual Studio, 然後在 [開始使用] 頁面上, 選取 [**複製或簽出程式碼**]:

1. 在 [**存放庫位置**] 中, 複製 [ [AZURE IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) ] 儲存機制:

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    您應該預期此作業需要幾分鐘的時間才能完成, 您可以在 [ **Team Explorer** ] 窗格中查看進度。

1. 在**Team Explorer**中開啟 [ **azure-iot-sdk-c** ] 存放庫, 選取 [**分支**], 搜尋 [**公開預覽**] 分支並將其簽出。

    ![公開預覽](media/howto-use-iot-device-workbench/vs-public-preview.png)

1. 將包含所產生程式碼的資料夾複製到裝置 SDK 根資料夾。

1. 在 VS 中開啟資料夾。`azure-iot-sdk-c`

1. 開啟裝置`CMakeLists.txt` SDK 根資料夾中的檔案。

1. 在`CMakeLists.txt`檔案結尾新增下列這一行, 以在您編譯 SDK 時包含裝置程式碼 stub 資料夾:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. 在 [**一般**] 工具列中,尋找 [設定] 下拉式清單。 選取 [**管理**設定] 以新增專案的 CMake 設定。

    ![管理設定](media/howto-use-iot-device-workbench/vs-manage-config.png)

1. 在 [ **CMake 設定**] 中, 新增新的設定, 並選取 [ **x64 發行**] 做為 [目標]。

1. 在**CMake 命令引數**中, 新增下列程式程式碼:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. 儲存檔案。

1. 在 **方案總管**中, 以滑鼠右鍵按一下`CMakeLists.txt`根資料夾中的, 然後從內容功能表中選取 **建立**, 以建立裝置 SDK 和產生的程式程式碼端。

1. 組建成功之後, 在命令提示字元中, 執行應用程式, 並將 IoT 中樞裝置連接字串指定為參數。

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> 若要深入瞭解如何在 Visual Studio 中使用 CMake, 請參閱[組建 CMake 專案](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects)。

### <a name="macos"></a>macOS

下列步驟示範如何使用 CMake 在 macOS 上建立裝置程式碼和裝置 C SDK:

1. 開啟 [終端機應用程式]。

1. 使用[Homebrew](https://homebrew.sh)來安裝所有相依性:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. 確認[CMake](https://cmake.org/)至少為版本**2.8.12**:

    ```bash
    cmake --version
    ```

1. [修補程式會捲曲](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os)到可用的最新版本。

1. 複製[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)存放庫:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    預期此作業需要幾分鐘的時間才能完成。

1. 將包含所產生程式碼的資料夾複製到裝置 SDK 根資料夾。

1. 在 VS Code 中, 開啟`CMakeLists.txt`裝置 SDK 根資料夾中的檔案。

1. 在`CMakeLists.txt`檔案結尾新增下列這一行, 以在您編譯 SDK 時包含裝置程式碼 stub 資料夾:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. 在裝置 SDK 根`cmake`資料夾中建立名為的資料夾, 然後流覽至該資料夾。

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令, 以使用 CMake 來建立裝置 SDK 和產生的程式程式碼端:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .

1. After the build succeeds, run the application specifying the IoT Hub device connection string as parameter.

    ```bash
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>重新產生基本架構程式碼以進行反覆運算

如果您更新您的 DCM 或介面檔案, 程式碼產生器可以重新產生程式碼。 假設您已經從 DCM 檔案產生您的裝置程式碼, 以重新產生程式碼:

1. 開啟具有 DCM 檔案的資料夾後, 使用**Ctrl + Shift + P**開啟命令選擇區, 輸入**IoT 隨插即用**, 然後選取 [**產生裝置程式碼 Stub**]。

1. 選擇您更新的 DCM 檔案。

1. 選取 [**重新產生 {project name} 的程式碼**]。

1. 程式碼產生器會使用您設定的先前設定, 並重新產生程式碼。 不過, 它不會覆寫可能包含使用者程式碼 (例如`main.c`和`{project_name}_impl.c`) 的檔案。

> [!NOTE]
> 如果您在介面檔案中更新 URN 識別碼, 則會將它視為新的介面。 當您重新產生程式碼時, 程式碼產生器會產生介面的程式碼, 但不會覆`{project_name}_impl.c`寫檔案中的原始檔案。

## <a name="problems-and-feedback"></a>問題與意見反應

Azure IoT 裝置工作臺擴充功能是 GitHub 上的開放原始碼專案。 針對任何問題和功能要求, 您可以[在 GitHub 上建立問題](https://github.com/microsoft/vscode-iot-workbench/issues)。

## <a name="next-steps"></a>後續步驟

在此操作說明文章中, 您已瞭解如何使用 Azure IoT 裝置工作臺來撰寫 DCM 和介面檔案。 您也已瞭解如何產生基本架構 C 程式碼來執行裝置應用程式。 建議的下一個步驟是瞭解如何[安裝和使用 Azure IoT explorer](./howto-install-iot-explorer.md)工具。
