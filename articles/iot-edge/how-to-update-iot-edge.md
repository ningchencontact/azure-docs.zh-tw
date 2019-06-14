---
title: 更新裝置上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何更新 IoT Edge 裝置以執行最新版的安全性精靈和 IoT Edge 執行階段
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3b6327b9e05b039696cc1743fc2d16c5e945e26
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65152625"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全性精靈和執行階段

IoT Edge 服務版本新版本，您會想要更新您的 IoT Edge 裝置的最新功能和安全性增強功能。 本文提供在新版本發行時如何更新 IoT Edge 裝置的相關資訊。 

如果您想要更新至較新的版本，IoT Edge 裝置將有兩個元件需要更新。 第一個元件是在裝置上執行，並在裝置啟動時啟動執行階段模組的安全性精靈。 目前，安全性精靈只能從裝置本身進行更新。 第二個元件是由 IoT Edge 中樞和 IoT Edge 代理程式模組組成的執行階段。 根據您建構部署的方式，執行階段可以從裝置或從遠端更新。 

若要尋找最新版的 Azure IoT Edge，請參閱 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

>[!IMPORTANT]
>如果您在 Windows 裝置上執行 Azure IoT Edge，在下列其中一項適用於您的裝置時，請勿更新為版本 1.0.5： 
>* 您尚未將您的裝置升級到 Windows 組建 17763。 IoT Edge 版本 1.0.5 不支援 17763 以前的 Windows 組建。
>* 您在 Windows 裝置上執行 Java 或 Node.js 模組。 即使您已經將您的 Windows 裝置更新為最新的組建，也請跳過版本 1.0.5。 
>
>如需有關 IoT Edge 版本 1.0.5 的詳細資訊，請參閱 [1.0.5 版本資訊](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5)。 如需如何更新為最新版本時，防止您的開發工具的詳細資訊，請參閱[IoT 開發人員部落格](https://devblogs.microsoft.com/iotdev/)。


## <a name="update-the-security-daemon"></a>更新安全性精靈

IoT Edge 安全性精靈是一項原生元件，必須在 IoT Edge 裝置上使用套件管理員進行更新。 

請使用 `iotedge version` 命令檢查在您的裝置上執行的安全性精靈版本。 

### <a name="linux-devices"></a>Linux 裝置

在 Linux 裝置上，請使用 apt-get 或適當的套件管理員來更新安全性精靈。 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Windows 裝置

在 Windows 裝置上使用 PowerShell 指令碼來更新的安全性服務精靈。 指令碼自動提取最新安全性精靈版本。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

執行更新 IoTEdge 命令，是在您的裝置，以及兩個執行階段容器映像中移除的安全性服務精靈。 Config.yaml 檔案會保留上的裝置，以及資料從白鯨容器引擎，（如果您使用 Windows 容器）。 讓您不必提供連接字串或您的裝置一次在更新程序期間的裝置佈建服務資訊的組態資訊方法。 

如果您想要安裝的安全性服務精靈的特定版本，下載適當的 Microsoft-Azure-IoTEdge.cab 檔案，從[IoT Edge 釋放](https://github.com/Azure/azure-iotedge/releases)。 然後，使用 `-OfflineInstallationPath` 參數指向該檔案位置。 如需詳細資訊，請參閱[離線指南](how-to-install-iot-edge-windows.md#offline-installation)。

## <a name="update-the-runtime-containers"></a>更新執行階段容器

您更新 IoT Edge 代理程式和 IoT Edge 中樞容器的方式，取決於您在部署中使用的是累積標記 (例如 1.0) 還是特定標記 (例如 1.0.2)。 

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令，檢查您裝置上目前的 IoT Edge 代理程式和 IoT Edge 中樞模組版本。 

  ![在記錄中尋找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 標記

IoT Edge 代理程式和 IoT Edge 中樞映像會標示與其相關聯的 IoT Edge 版本。 標記可透過兩種不同的使用方式用於執行階段映像上： 

* **累積標記** - 僅使用版本號碼的前兩個值來取得符合這些數字的最新映像。 例如，有新版本指向最新的 1.0.x 版時，就會更新 1.0。 如果 IoT Edge 裝置的容器執行階段重新提取映像，執行階段模組就會更新為最新版本。 進行開發時建議使用此方法。 從 Azure 入口網站執行的部署預設為累積標記。 
* **特定標記** - 版本號碼的三個值全都會使用，以明確設定映像版本。 例如，1.0.2 在其初始版本後將不會變更。 當您準備好要更新時，您可以在部署資訊清單中宣告新的版本號碼。 建議將此方法用於生產用途。

### <a name="update-a-rolling-tag-image"></a>更新累積標記映像

如果您在部署中使用累積標記 (例如 mcr.microsoft.com/azureiotedge-hub:**1.0**)，您就必須在裝置上強制執行容器執行階段以提取映像的最新版本。 

從您的 IoT Edge 裝置中刪除映像的本機版本。 在 Windows 機器上，解除安裝安全性精靈也會移除執行階段映像，因此您不需要再採取此步驟。 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

您可能需要使用強制 `-f` 旗標將映像移除。 

IoT Edge 服務將會提取執行階段映像的最新版本，並自動在您的裝置上將其重新啟動。 

### <a name="update-a-specific-tag-image"></a>更新特定標記映像

如果您在部署中使用特定標記 (例如 mcr.microsoft.com/azureiotedge-hub:**1.0.2**)，則只需要更新部署資訊清單中的標記，並將變更套用至裝置即可。 

在 Azure 入口網站中，執行階段部署映像會宣告於 [設定進階 Edge 執行階段設定]  區段中。 

![設定進階的 edge 執行階段設定](./media/how-to-update-iot-edge/configure-runtime.png)

在 JSON 部署資訊清單中，更新 [systemModules]  區段中的模組映像。 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>後續步驟

檢視最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持續關注[物聯網部落格](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和公告 