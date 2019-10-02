---
title: 設定環境 - Azure IoT Edge 上的 Machine Learning | Microsoft Docs
description: 針對用來在邊緣進行機器學習之模組的開發和部署準備您的環境。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1db94e683a0dfb3b60b12bc5ac205c766d405d0a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299832"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>教學課程：在 IoT Edge 上為機器學習設定環境

> [!NOTE]
> 此文章是關於在 IoT Edge 上使用 Azure Machine Learning 的系列文章之一。 如果您是被直接引導至這篇文章，我們建議您先從本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)開始，以取得最佳成效。

這篇來自在 IoT Edge 上進行端對端 Azure Machine Learning 之教學課程的文章，可協助您針對開發和部署準備環境。 首先，請使用所需的所有工具設定開發機器。 接下來，在 Azure 中建立必要的雲端資源。

## <a name="set-up-a-development-machine"></a>設定開發機器

此步驟通常是由雲端開發人員來執行。 某一部分的軟體可能也會對資料科學家有所幫助。

在此文章期間，我們將會執行數個開發人員工作，包括針對 IoT Edge 模組和 IoT 裝置進行程式碼撰寫、編譯、設定及部署。 為了方便使用，我們已建立能建立已設定好許多必要條件之 Azure 虛擬機器的 PowerShell 指令碼。 我們建立的 VM 必須能處理[巢狀虛擬化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)，這也是我們選擇 [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1) 電腦大小的原因。

開發 VM 將會搭配下列項目進行設定：

* Windows 10
* [Chocolatey](https://chocolatey.org/) \(英文\)
* [適用於 Windows 的 Docker Desktop](https://www.docker.com/products/docker-desktop) \(英文\)
* [Git for Windows](https://gitforwindows.org/)
* [適用於 Windows 的 Git 認證管理員](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) \(英文\)
* [.Net Core SDK](https://dotnet.microsoft.com/) (英文)
* [Python 3](https://www.python.org/) \(英文\)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code 擴充](https://marketplace.visualstudio.com/search?target=VSCode) \(英文\)
  * [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

開發人員 VM 並非絕對必要，因為所有的開發工具都可以在本機電腦上執行。 不過，我們強烈建議您使用 VM 來確保環境能保持一致。

建立及設定虛擬機器只需要大約 30 分鐘的時間。

### <a name="get-the-script"></a>取得指令碼

從[機器學習和 IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) \(英文\) 範例存放庫複製或下載 PowerShell 指令碼。

### <a name="create-an-azure-virtual-machine"></a>建立 Azure 虛擬機器

DevVM 目錄包含建立適合完成此教學課程之 Azure 虛擬機器的所需檔案。

1. 以系統管理員身分開啟 Powershell，然後瀏覽至您下載程式碼的目錄。 我們會將來源的根目錄稱為 `<srcdir>`。

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. 執行下列命令來允許執行指令碼。 出現提示時，請選擇 [全部皆是]  。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. 從這個目錄執行 Create-AzureDevVM.ps1。

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * 出現提示時，請提供下列資訊：
      * **Azure 訂用帳戶識別碼**：您的訂用帳戶識別碼，其可在 Azure 入口網站中找到
      * **資源群組名稱**：Azure 中新的或現有資源群組的名稱
      * **位置**：選擇要建立虛擬機器的 Azure 位置。 例如 westus2 或 northeurope。 如需詳細資訊，請參閱 [Azure 的地點](https://azure.microsoft.com/global-infrastructure/locations/)。
      * **AdminUsername**：針對您想要建立並用於虛擬機器的系統管理員帳戶提供容易記住的名稱。
      * **AdminPassword**：針對虛擬機器上的系統管理員帳戶設定密碼。

    * 如果您尚未安裝 Azure PowerShell，指令碼將會安裝 [Azure PowerShell Az 模組](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0)

    * 系統將提示您登入 Azure。

    * 指令碼將會確認建立 VM 的資訊。 請按 `y` 或 `Enter` 來繼續。

指令碼會執行下列步驟，這需要花費數分鐘的時間：

* 建立資源群組 (若它尚不存在)
* 部署虛擬機器
* 在 VM 上啟用 Hyper-V
* 安裝開發及複製範例存放庫所需的軟體
* 重新啟動 VM
* 在桌面上建立 RDP 檔案以連線至 VM

### <a name="set-auto-shutdown-schedule"></a>設定自動關機排程

為了協助您降低成本，將會搭配自動關機排程來建立 VM，並將排程設定為 1900 PST。 根據您的位置和排程，您可能需要更新此時間。 更新關機排程：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至您在上個小節中所提供之資源群組中的虛擬機器。

3. 選取側導覽器上的 [自動關機]  。

4. 在 [已排程關機]  中輸入新的關機時間，或是變更 [時區]  ，然後按一下 [儲存]  。

### <a name="connect-and-configure-development-machine"></a>連線並設定開發電腦

在建立好 VM 之後，我們現在必須安裝完成此教學課程所需的軟體。

#### <a name="start-a-remote-desktop-session"></a>啟動遠端桌面工作階段

1. VM 建立指令碼已在您的桌面上建立 RDP 檔案。

2. 按兩下具有 **\<Azure VM 名稱\>.rdp** 作為名稱的檔案。

3. 系統會顯示對話方塊，說明遠端連線的發行者為未知的發行者。 請按一下 [不要再詢問我是否要連線到這部電腦]  核取方塊，然後選取 [連線]  。

4. 出現提示時，請提供您在執行指令碼以設定 VM 時所使用的 AdminPassword，然後按一下 [確定]  。

5. 系統會提示您接受 VM 的憑證。 請選取 [不要再詢問我是否要連線到這部電腦]  ，然後選擇 [是]  。

#### <a name="install-visual-studio-code-extensions"></a>安裝 Visual Studio Code 擴充

在您連線至開發電腦之後，請為 Visual Studio Code 加入一些有用的擴充，來使開發體驗變得更加輕鬆。

1. 在 PowerShell 視窗中，瀏覽至 **C:\\source\\IoTEdgeAndMlSample\\DevVM**。

2. 輸入下列內容來允許在虛擬機器上執行指令碼。

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. 執行指令碼。

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. 指令碼將會執行幾分鐘的時間來安裝 VS Code 擴充：

    * Azure IoT Tools
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>設定 IoT 中樞和儲存體

這些步驟通常是由雲端開發人員來執行。

Azure IoT 中樞是所有 IoT 應用程式的核心。 它會處理 IoT 裝置和雲端之間的安全通訊。 它是 IoT Edge 機器學習解決方案作業的主要協調點。

* IoT 中樞會使用路由將來自 IoT 裝置的傳入資料導向其他下游服務。 我們會利用 IoT 中樞路由來將裝置資料傳送至 Azure 儲存體，讓 Azure Machine Learning 可在其中使用該資料，以對我們的剩餘使用年限 (RUL) 分類器進行定型。

* 稍後在教學課程中，我們將會使用 IoT 中樞來設定及管理 Azure IoT Edge 裝置。

在此節中，您會使用指令碼來建立 Azure IoT 中樞及 Azure 儲存體帳戶。 接著，您會使用 Azure 入口網站來設定能將中樞所接收到的資料轉送至 Azure 儲存體 Blob 容器的路由。 這些步驟需要約 10 分鐘來完成。

### <a name="create-cloud-resources"></a>建立雲端資源

1. 在開發電腦上開啟 PowerShell 視窗。

1. 變更為 IoTHub 目錄。

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. 執行建立指令碼。 使用和您在建立開發 VM 時相同的值來設定訂用帳戶識別碼、位置及資源群組。

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * 系統將提示您登入 Azure。
    * 指令碼會確認建立中樞和儲存體帳戶的資訊。 請按 `y` 或 `Enter` 來繼續。

指令碼需要約兩分鐘的時間來執行。 完成後，指令碼會輸出中樞和儲存體帳戶的名稱。

### <a name="review-route-to-storage-in-iot-hub"></a>檢閱針對 IoT 中樞中儲存體的路由

作為建立 IoT 中樞的一部分，我們在上一節中所執行的指令碼也已建立自訂端點和路由。 IoT 中樞路由包含查詢運算式和端點。 如果訊息符合運算式，資料便會沿著路由被傳送至相關聯的端點。 端點可以是事件中樞、服務匯流排佇列及主題。 在此案例中，端點是儲存體帳戶中的 Blob 容器。 讓我們使用 Azure 入口網站來檢閱由指令碼所建立的路由。

1. 開啟 [Azure 入口網站](https://portal.azure.com)。

1. 從左側導覽器選擇所有服務，在搜尋方塊中輸入 IoT，然後選取 [IoT 中樞]  。

1. 選擇在上一個步驟中所建立的 IoT 中樞。

1. 在 IoT 中樞側導覽器中，選擇 [訊息路由]  。

1. 訊息路由頁面具有兩個索引標籤，[路由]  和 [自訂端點]  。 選取 [自訂端點]  索引標籤。

1. 在 [Blob 儲存體]  底下，選取 **turbofanDeviceStorage**。

1. 請注意，此端點會指向在上一個步驟中於儲存體帳戶中建立，名為 **devicedata** 的 Blob 容器；該儲存體帳戶的名稱為 **iotedgeandml\<唯一尾碼\>** 。

1. 也請注意，[Blob 檔案名稱格式]  已經從預設格式變更為將分割區作為名稱最後元素的格式。 我們認為這個格式對於我們稍後會在教學課程中搭配 Azure Notebooks 進行的檔案作業來說較為方便。

1. 關閉端點詳細資料刀鋒視窗，然後返回 [訊息路由]  頁面。

1. 選取 [路由]  索引標籤。

1. 選取名為 **turbofanDeviceDataToStorage** 的路由。

1. 請注意，錄由的端點是 **turbofanDeviceStorage** 自訂端點。

1. 查看路由查詢，其已被設定為 **true**。 這代表所有裝置遙測訊息都將符合此路由，這會使所有訊息都被傳送至 **turbofanDeviceStorage** 端點。

1. 關閉路由詳細資料。

## <a name="next-steps"></a>後續步驟

在此文章中，我們已建立 IoT 中樞，並設定針對 Azure 儲存體帳戶的路由。 在下一篇文章中，我們將會透過 IoT 中樞將來自一組模擬裝置的資料傳送至儲存體帳戶。 稍後在教學課程中，當我們已設定 IoT Edge 裝置和模組之後，我們將會重新造訪路由，並進一步查看路由查詢。

如需這部分 IoT Edge 上的 Machine Learning 教學課程所涵蓋之步驟的詳細資訊，請參閱：

* [Azure IoT 基礎](https://docs.microsoft.com/azure/iot-fundamentals/)
* [使用 IoT 中樞設定訊息路由](../iot-hub/tutorial-routing.md)
* [使用 Azure 入口網站建立 IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)

請前往下一篇文章以建立模擬裝置以進行監視。

> [!div class="nextstepaction"]
> [產生裝置資料](tutorial-machine-learning-edge-03-generate-data.md)
