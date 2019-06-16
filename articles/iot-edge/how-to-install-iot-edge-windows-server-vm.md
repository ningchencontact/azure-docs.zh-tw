---
title: Windows Server 虛擬機器上執行 Azure IoT Edge |Microsoft Docs
description: Azure IoT Edge 的安裝指示在 Windows Server Marketplace 虛擬機器
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: gregman
ms.openlocfilehash: 9e3f7e3b23cba3fab87ee35aa2a15b6305d9ece4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054189"
---
# <a name="run-azure-iot-edge-on-windows-server-virtual-machines"></a>Windows Server 虛擬機器上執行 Azure IoT Edge
Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 此執行階段可以部署在像 Raspberry Pi 一樣小或像工業伺服器一樣大的裝置上。 利用 IoT Edge 執行階段設定裝置之後，您就可以開始從雲端將商務邏輯部署給它。

若要深入了解 IoT Edge 執行階段的運作方式，以及會包含哪些元件，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。

這篇文章列出執行 Windows Server 2019 的虛擬機器使用的 Azure IoT Edge 執行階段的步驟[Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace 供應項目。 請遵循指示[安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-windows.md)搭配其他版本的 Windows 上。

## <a name="deploy-from-the-azure-marketplace"></a>從 Azure Marketplace 進行部署
1.  瀏覽至[Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview) Azure Marketplace 供應項目或搜尋"Windows Server" [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  選取**取得立即** 
3.  在 [**軟體計劃**，尋找 [Windows Server 2019 資料中心伺服器核心與容器]，然後選取**繼續**在下一步] 對話方塊。
    * 您也可以使用這些指示的其他版本的 Windows Server 容器
4.  一旦進入 Azure 入口網站，選取 [建立]  並且遵循精靈以部署 VM。 
    *   如果這是您第一次試用 VM，則更加容易使用的密碼，並啟用 RDP 和 SSH 公用輸入連接埠 功能表中。 
    *   如果您有資源密集工作負載，您應該藉由新增更多的 CPU 和/或記憶體來升級虛擬機器大小。
5.  一旦部署虛擬機器，將它設定為連線到您的 IoT 中樞：
    1.  從您在 IoT 中樞建立的 IoT Edge 裝置複製裝置連接字串 (如果您還未熟悉這個程序，可以遵循[從 Azure 入口網站註冊新的 Azure IoT Edge 裝置](how-to-register-device-portal.md)操作指南)
    1.  從 Azure 入口網站選取您新建立的虛擬機器資源，然後開啟**執行命令**選項
    1.  選取  **RunPowerShellScript**選項
    1.  此指令碼複製到您的裝置連接字串的 [命令] 視窗中： 
        ```powershell
        . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
        Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'
        ```
    1.  執行指令碼安裝 Edge 執行階段，並選取以設定您的連接字串**執行**
    1.  一或兩分鐘之後, 您應該會看到訊息，確認已安裝 Edge 執行階段，並成功佈建。


## <a name="deploy-from-the-azure-portal"></a>從 Azure 入口網站部署
1. 從 Azure 入口網站中，搜尋 「 Windows 伺服器 」 並選取**Windows Server 2019 Datacenter**開始建立 VM 的工作流程。 
2. 從**選取軟體計劃**選擇 「 Windows Server 2019 Datacenter Server Core 與容器 」，然後選取 **建立**
3. 完成上述步驟 5 中 「 部署從 Azure Marketplace 」 指示。

## <a name="deploy-from-azure-cli"></a>從 Azure CLI 進行部署
1. 如果您在桌面上使用 Azure CLI，從登入下列位置開始：

   ```azurecli-interactive
   az login
   ```
    
1. 如果您有多個訂用帳戶，請選取想要使用的訂用帳戶：
   1. 列出您的訂用帳戶：
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. 複製想要使用的訂用帳戶 SubscriptionID 欄位
   1. 使用您複製的識別碼，執行下列命令：
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. 建立新的資源群組 (或在下一個步驟中指定現有的資源群組)：

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. 建立新的虛擬機器：

   ```azurecli-interactive
   az vm create -g IoTEdgeResources -n EdgeVM --image MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-with-Containers:latest  --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```
   * 此命令會提示您輸入密碼，但您可以將選項`--admin-password`指令碼中更輕鬆地設定
   * Windows Server Core 映像具有命令行只能使用遠端桌面的支援，因此如果您想要完整桌面體驗，指定`MicrosoftWindowsServer:WindowsServer:2019-Datacenter-with-Containers:latest`作為映像

1. 設定裝置連接字串 (如果您還未熟悉這個程序，可以遵循[使用 Azure CLI 註冊新的 Azure IoT Edge 裝置](how-to-register-device-cli.md)操作指南)：

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunPowerShellScript --script ". {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `Install-IoTEdge -Manual -DeviceConnectionString '<connection-string>'"
   ```

## <a name="next-steps"></a>後續步驟

現在您已佈建 IoT Edge 裝置並安裝執行階段，接下來您可以[部署 IoT Edge 模組](how-to-deploy-modules-portal.md)。

如果您有正確安裝 Edge 執行階段的問題，請參閱[疑難排解](troubleshoot.md)頁面。

若要將現有安裝更新為最新版的 IoT Edge，請參閱[更新 IoT Edge 安全性精靈和執行階段](how-to-update-iot-edge.md)。

深入了解使用 Windows 虛擬機器[Windows 虛擬機器文件](https://docs.microsoft.com/azure/virtual-machines/windows/)。

如果您想透過 ssh 連線到此 VM 設定之後，請遵循[安裝的 OpenSSH 適用於 Windows Server](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse#installing-openssh-with-powershell)引導使用遠端桌面或遠端 powershell。
