---
title: 更新裝置上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何更新 IoT Edge 裝置以執行最新版的安全性精靈和 IoT Edge 執行階段
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 27f0ebab1fd87eb1870c5a8be21c4f80be4132f1
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698595"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全性精靈和執行階段

當 IoT Edge 服務發行新版本時, 您會想要更新您的 IoT Edge 裝置, 以取得最新的功能和安全性改進。 本文提供在新版本發行時如何更新 IoT Edge 裝置的相關資訊。 

如果您想要更新至較新的版本，IoT Edge 裝置將有兩個元件需要更新。 第一個元件是在裝置上執行，並在裝置啟動時啟動執行階段模組的安全性精靈。 目前，安全性精靈只能從裝置本身進行更新。 第二個元件是由 IoT Edge 中樞和 IoT Edge 代理程式模組組成的執行階段。 根據您建構部署的方式，執行階段可以從裝置或從遠端更新。 

若要尋找最新版的 Azure IoT Edge，請參閱 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="update-the-security-daemon"></a>更新安全性精靈

IoT Edge 安全性精靈是一項原生元件，必須在 IoT Edge 裝置上使用套件管理員進行更新。 

請使用 `iotedge version` 命令檢查在您的裝置上執行的安全性精靈版本。 

### <a name="linux-devices"></a>Linux 裝置

在 Linux x64 裝置上, 請使用 apt 取得或適當的套件管理員來更新安全性守護程式。 

```bash
apt-get update
apt-get install libiothsm iotedge
```

在 Linux ARM32 裝置上, 請使用在[linux 上安裝 Azure IoT Edge 執行時間 (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)中的步驟來安裝最新版的安全性守護程式。 

### <a name="windows-devices"></a>Windows 裝置

在 Windows 裝置上, 請使用 PowerShell 腳本來更新安全性守護程式。 腳本會自動提取最新版本的安全性守護程式。 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

執行 IoTEdge 命令會從您的裝置移除安全性背景程式, 以及兩個執行時間容器映射。 Yaml 檔案會保留在裝置上, 以及來自 Moby 容器引擎 (如果您使用的是 Windows 容器) 的資料。 保留設定資訊表示您不需要在更新程式期間, 再次提供裝置的連接字串或裝置布建服務資訊。 

如果您想要安裝特定版本的安全性守護程式, 請從[IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)下載適當的 Microsoft-Azure-IoTEdge .cab 檔案。 然後，使用 `-OfflineInstallationPath` 參數指向該檔案位置。 如需詳細資訊，請參閱[離線指南](how-to-install-iot-edge-windows.md#offline-installation)。

## <a name="update-the-runtime-containers"></a>更新執行階段容器

更新 IoT Edge 代理程式和 IoT Edge 中樞容器的方式, 取決於您是在部署中使用輪流標記 (例如 1.0) 還是特定標記 (例如 1.0.7)。 

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令，檢查您裝置上目前的 IoT Edge 代理程式和 IoT Edge 中樞模組版本。 

  ![在記錄中尋找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 標記

IoT Edge 代理程式和 IoT Edge 中樞映像會標示與其相關聯的 IoT Edge 版本。 標記可透過兩種不同的使用方式用於執行階段映像上： 

* **累積標記** - 僅使用版本號碼的前兩個值來取得符合這些數字的最新映像。 例如，有新版本指向最新的 1.0.x 版時，就會更新 1.0。 如果 IoT Edge 裝置的容器執行階段重新提取映像，執行階段模組就會更新為最新版本。 進行開發時建議使用此方法。 從 Azure 入口網站執行的部署預設為累積標記。 
* **特定標記** - 版本號碼的三個值全都會使用，以明確設定映像版本。 例如, 1.0.7 在其初始版本之後不會變更。 當您準備好要更新時，您可以在部署資訊清單中宣告新的版本號碼。 建議將此方法用於生產用途。

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

如果您在部署中使用特定標記 (例如, mcr.microsoft.com/azureiotedge-hub:**1.0.7**), 則您只需要更新部署資訊清單中的標記, 並將變更套用至您的裝置。 

在 Azure 入口網站中，執行階段部署映像會宣告於 [設定進階 Edge 執行階段設定] 區段中。 

![設定 advanced edge 執行時間設定](./media/how-to-update-iot-edge/configure-runtime.png)

在 JSON 部署資訊清單中，更新 [systemModules] 區段中的模組映像。 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>更新為發行候選版本

Azure IoT Edge 定期發行 IoT Edge 服務的新版本。 在每個穩定版本之前, 都有一或多個候選版 (RC) 版本。 RC 版本包含發行的所有規劃功能, 但仍在進行穩定版本所需的測試和驗證程式。 如果您想要及早測試新功能, 您可以安裝 RC 版本, 並透過 GitHub 提供意見反應。 

發行候選版本遵循相同的版本編號慣例, 但具有 **-rc**加上結尾附加的遞增編號。 您可以在與穩定版本相同的[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)清單中看到發行候選版本。 例如, 尋找**1.0.7 rc1**和**1.0.7-rc2**, 這是**1.0.7**之前的兩個候選版本。 您也可以看到 RC 版本是以**發行前**標籤標示。 

做為預覽, 發行候選版本不會包含為一般安裝程式目標的最新版本。 相反地, 您需要手動將您想要測試的 RC 版本資產設為目標。 根據您 IoT Edge 的裝置作業系統, 請使用下列各節將 IoT Edge 更新為特定版本:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>後續步驟

檢視最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持續關注[物聯網部落格](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和公告 