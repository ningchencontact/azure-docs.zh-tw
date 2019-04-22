---
title: 如何部署 OPC 保存庫模組從頭-Azure |Microsoft Docs
description: 如何從零開始部署 OPC 保存庫。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dedba42d7e4b8d603d97522cf0173f41efd20b3a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496891"
---
# <a name="deploy-opc-vault-from-scratch"></a>從頭開始部署 OPC 保存庫

OPC 保存庫是微服務，可以設定、 註冊和管理憑證生命週期的 OPC UA 伺服器和雲端中的用戶端應用程式。 這篇文章會示範如何從頭部署 OPC 保存庫。

## <a name="configuration-and-environment-variables"></a>組態和環境變數

服務組態會儲存在 appsettings.ini 使用 ASP.NET Core 組態的配接器。 INI 格式允許將值儲存在可讀取的格式，隨附註解。
應用程式也支援插入環境變數，例如認證和網路的詳細資料。 （本節原本稱為 TODO 組態和環境變數）。

存放庫中的組態檔會參考一些環境變數，必須建立至少一次。 根據您的作業系統和 IDE，有數種方式可管理環境變數：

- Windows 使用者，讓 env-變數-setup.cmd 指令碼必須準備和執行一次。 在執行時，設定會保存在終端機工作階段並重新開機。

- 對於 Linux 和 OSX 的環境，環境變數設定指令碼需要每次開啟新的主控台執行。 根據 OS 和終端機中，有全域，如需詳細資訊，這些頁面應可協助保存值的方式：

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio：環境變數可以也設定從 Visual Studio，然後按 專案屬性，在左窗格中選取 組態屬性 及 環境 區段，您可以在其中加入多個變數。

- IntelliJ Rider:可以在每個執行設定中，類似於 IntelliJ IDEA 設定環境變數 https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>執行和偵錯使用 Visual Studio

Visual Studio 可讓您快速開啟應用程式，而不使用命令提示字元，而不需要設定在 IDE 外面的任何項目。

使用 Visual Studio 2017 的步驟：

1. 開啟方案使用`iot-opc-gds-service.sln`檔案。

1. 載入方案時，以滑鼠右鍵按一下`WebService`專案，選取，並移至`Debug`一節。

1. 在相同的區段中，定義所需的環境變數。

1. 按下**F5**，或有**執行**圖示。 Visual Studio 應該會開啟瀏覽器以 JSON 格式顯示服務狀態。

## <a name="run-and-debug-with-intellij-rider"></a>執行和偵錯與 IntelliJ Rider

1. 開啟方案使用`iot-opc-gds-service.sln`檔案。

1. 載入方案時，請移至`Run > Edit Configurations`並建立新`.NET Project`組態。

1. 在組態中，選取 web 服務專案。

1. 儲存設定並執行建立從 IDE 工具列中的組態。

1. 您應該會看到服務啟動 IntelliJ 執行視窗與詳細資料，例如 URL 加上服務的 web 服務正在執行中的訊息記錄檔。

## <a name="build-and-run-from-the-command-line"></a>從命令列建置和執行

指令碼 資料夾包含一些常見的工作的指令碼：

- `build`:編譯的所有專案，並執行測試。

- `compile`:編譯的所有專案。

- `run`:編譯專案並執行的服務，也會提示提高權限的權限，在 Windows 中執行 web 服務。

指令碼會檢查環境變數設定。 您可以在您的作業系統，全域設定環境變數，或使用指令碼資料夾中的 「 環境變數-安裝程式 」 指令碼。

### <a name="sandbox"></a>沙箱

指令碼會假設您使用.NET Core 和 Docker 設定您的開發環境。 您可以避免安裝.NET Core 和安裝只有 Docker，並使用命令列參數`--in-sandbox`(或簡短形式`-s`)，例如：

- `build --in-sandbox`:執行 Docker 容器內部建置工作 (簡短形式`build -s`)。

- `compile --in-sandbox`:執行 Docker 容器內部的編譯工作 (簡短形式`compile -s`)。

- `run --in-sandbox`:啟動 Docker 容器內部的服務 (簡短形式`run -s`)。

Docker 映像用於沙箱會裝載於 Docker Hub[此處](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet)。

## <a name="package-the-application-to-a-docker-image"></a>封裝應用程式的 Docker 映像

`scripts`資料夾包含 docker 子資料夾與封裝成 Docker 映像的服務所需的檔案：

- `Dockerfile`:Docker 映像規格。
- `build`:建置 Docker 容器，並儲存在本機登錄的映像。
- `run`:從映像儲存在本機登錄中執行 Docker 容器。
- `content`:具有檔案複製到映像，包括進入點指令碼的資料夾。

## <a name="azure-iot-hub-setup"></a>Azure IoT 中樞設定

若要使用微服務，設定您的 Azure IoT 中樞進行開發和整合測試。

專案包含一些可協助您進行此安裝程式的 Bash 指令碼：

- 建立新的 IoT 中樞： `./scripts/iothub/create-hub.sh`

- 列出現有的中樞： `./scripts/iothub/list-hubs.sh`

- 顯示詳細資料 （例如，索引鍵） 的 IoT 中樞： `./scripts/iothub/show-hub.sh`

然後，如果您有多個 Azure 訂用帳戶：

- 顯示訂用帳戶清單： `./scripts/iothub/list-subscriptions.sh`

- 變更目前的訂用帳戶： `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>開發設定

### <a name="net-setup"></a>.NET 安裝程式

透過管理專案工作流程[.NET Core](https://dotnet.github.io) 1.x，您必須安裝在您的環境中，您可以執行所有指令碼，並確保您的 IDE 會在如預期般運作。

我們也會提供[Java 版本](https://github.com/Azure/iot-opc-gds-service-dotnet)這個專案及其他 Azure IoT 電腦元件。

### <a name="ide"></a>IDE

以下是 ide 的一些您可以使用 Azure IoT 的電腦上運作:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Git 安裝

專案包含 Git 攔截程序自動執行一些檢查之前接受的程式碼變更。 您可以手動執行測試，或讓 CI 平台，來執行測試。 我們可以使用下列 Git 勾點來自動執行所有測試設定，之後再傳送至 GitHub 的程式碼變更，並加速開發工作流程。

如果在任何時間點，您想要移除的攔截程序，只刪除檔案安裝在`.git/hooks`。 您可以同時略過的認可前攔截程序使用`--no-verify`選項。

#### <a name="pre-commit-hook-with-sandbox"></a>與沙箱的認可前攔截程序

若要設定包含的勾點，開啟 Windows/Linux/MacOS 主控台並執行：

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

使用此設定時，簽入檔案時，Git 會確認應用程式通過所有測試，執行組建，並已使用所有的開發需求的 Docker 容器內部的測試。

#### <a name="pre-commit-hook-without-sandbox"></a>認可前攔截程序，而不需要沙箱

> [!NOTE] 
> 沒有沙箱的勾點要求[.NET Core](https://dotnet.github.io)位於系統路徑。

若要設定包含的勾點，開啟 Windows/Linux/MacOS 主控台並執行：

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
使用此設定時，簽入檔案時，Git 會確認應用程式通過所有測試，在您的工作站使用您的作業系統中安裝的工具中執行組建和測試。

專案程式碼樣式的指引：

- 在合理的狀況下，行長度是限制為 80 個字元以協助程式碼檢閱和命令列的編輯器。

- 程式碼區塊使用四個空格縮排。 應避免的索引標籤字元。

- 文字檔案使用行格式 (LF) 的 Unix 的端。

- 使用管理相依性插入[Autofac](https://autofac.org)。

- Web 服務 Api 欄位是 CamelCased 除了中繼資料。

## <a name="next-steps"></a>後續步驟

既然您已了解如何從頭部署 OPC 保存庫，以下是建議的下一個步驟：

> [!div class="nextstepaction"]
> [從頭部署 OPC 對應項](howto-opc-twin-deploy-modules.md)