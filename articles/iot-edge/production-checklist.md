---
title: 針對生產環境準備裝置和部署 - Azure IoT Edge | Microsoft Docs
description: Learn how to take your Azure IoT Edge solution from development to production, including setting up your devices with the appropriate certificates and making a deployment plan for future code updates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d8ba8452f5f2d4ab05083e1a97fa0b9ba75017f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457316"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>準備在生產環境中部署 IoT Edge 解決方案

當您準備將 IoT Edge 解決方案從開發層面轉移到生產環境時，請確保其設定可以提供持續性效能。

The information provided in this article isn't all equal. 為了協助您設定優先權，每個區段都會以清單開始，並將工作分成兩個部分：生產前需完成的**重要**事項，或是您需要知道的**實用**事項。

## <a name="device-configuration"></a>裝置設定

IoT Edge 裝置可以是任何項目，包括 Raspberry Pi、膝上型電腦或伺服器上執行的虛擬機器。 您可以實際或透過虛擬連線存取裝置，或者裝置可能長時間受到隔離。 Either way, you want to make sure that it's configured to work appropriately. 

* **重要**
    * 安裝生產憑證
    * 研擬裝置管理計畫
    * 使用 Moby 作為容器引擎

* **實用**
    * 選擇上游通訊協定

### <a name="install-production-certificates"></a>安裝生產憑證

生產環境中的每個 IoT Edge 裝置都要安裝裝置憑證授權單位 (CA) 的憑證。 此 CA 憑證接著會在 config.yaml 檔案的 IoT Edge 執行階段中宣告。 為了簡化開發和測試流程，如果 config.yaml 檔案中沒有任何宣告的憑證，IoT Edge 執行階段會建立暫時憑證。 不過，這些暫時憑證三個月後就會過期，而且對於生產情節並不安全。 

若要了解裝置 CA 憑證的角色，請參閱 [Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

如需如何在 IoT Edge 裝置上安裝憑證，並參考 config.yaml 檔案中的憑證等詳細資訊，請參閱[設定 IoT Edge 裝置作為透明閘道](how-to-create-transparent-gateway.md)。 無論裝置是否作為閘道使用，設定憑證的步驟都相同。 本文為了建立範例憑證所提供的指令碼僅供測試之用。 請勿在生產環境中使用這些範例憑證。 

### <a name="have-a-device-management-plan"></a>研擬裝置管理計畫

在生產環境中設置任何裝置前，應先了解如何管理未來的更新作業。 若是 IoT Edge 裝置，要更新的元件清單可能包含：

* 裝置軔體
* 作業系統程式庫
* 容器引擎，例如 Moby
* IoT Edge 精靈
* CA 憑證

For more information, see [Update the IoT Edge runtime](how-to-update-iot-edge.md). 目前更新 IoT Edge 精靈的方法需要實際存取或透過 SSH 存取 IoT Edge 裝置。 If you have many devices to update, consider adding the update steps to a script or use an automation tool like Ansible.

### <a name="use-moby-as-the-container-engine"></a>使用 Moby 作為容器引擎

A container engine is a prerequisite for any IoT Edge device. 生產環境僅支援 Moby 引擎。 Docker 等其他容器引擎可搭配 IoT Edge 使用，也可以使用這些引擎進行開發。 搭配 Azure IoT Edge 使用時可重新分配 Moby 引擎，而且 Microsoft 可提供此引擎的相關服務。

### <a name="choose-upstream-protocol"></a>選擇上游通訊協定

The protocol (and therefore the port used) for upstream communication to IoT Hub can be configured for both the IoT Edge agent and the IoT Edge hub. 預設的通訊協定是 AMQP，但您可以根據網路設定變更。 

這兩個執行階段模組都有 **UpstreamProtocol** 環境變數。 有效的變數值為： 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure the UpstreamProtocol variable for the IoT Edge agent in the config.yaml file on the device itself. For example, if your IoT Edge device is behind a proxy server that blocks AMQP ports, you may need to configure the IoT Edge agent to use AMQP over WebSocket (AMQPWS) to establish the initial connection to IoT Hub. 

一旦您的 IoT Edge 裝置連線，請務必在未來的部署中繼續設定這兩個執行階段模組的 UpstreamProtocol 變數。 [設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

## <a name="deployment"></a>Deployment

* **實用**
    * 與上游的通訊協定一致
    * Set up host storage for system modules
    * Reduce memory space used by the IoT Edge hub
    * 請勿使用模組映像的偵錯版本

### <a name="be-consistent-with-upstream-protocol"></a>與上游的通訊協定一致

If you configured the IoT Edge agent on your IoT Edge device to use a different protocol than the default AMQP, then you should declare the same protocol in all future deployments. 舉例來說，如果您的 IoT Edge 裝置位於封鎖 AMQP 連接埠的 Proxy 伺服器後方，可能是設定該裝置透過 WebSocket (AMQPWS) 使用 AMQP 連線。 When you deploy modules to the device, configure the same AMQPWS protocol for the IoT Edge agent and IoT Edge hub, or else the default AMQP will override the settings and prevent you from connecting again. 

You only have to configure the UpstreamProtocol environment variable for the IoT Edge agent and IoT Edge hub modules. 任何其他模組會採用執行階段模組中設定的任何通訊協定。 

[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

### <a name="set-up-host-storage-for-system-modules"></a>Set up host storage for system modules

The IoT Edge hub and agent modules use local storage to maintain state and enable messaging between modules, devices, and the cloud. For better reliability and performance, configure the system modules to use storage on the host filesystem.

For more information, see [Host storage for system modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduce memory space used by IoT Edge hub

If you're deploying constrained devices with limited memory available, you can configure IoT Edge hub to run in a more streamlined capacity and use less disk space. These configurations do limit the performance of the IoT Edge hub, however, so find the right balance that works for your solution. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>請勿在受限裝置上進行效能最佳化

The IoT Edge hub is optimized for performance by default, so it attempts to allocate large chunks of memory. 這項設定會在 Raspberry Pi 等小型裝置上造成穩定性問題。 If you're deploying devices with constrained resources, you may want to set the **OptimizeForPerformance** environment variable to **false** on the IoT Edge hub. 

When **OptimizeForPerformance** is set to **true**, the MQTT protocol head uses the PooledByteBufferAllocator which has better performance but allocates more memory. The allocator does not work well on 32 bit operating systems or on devices with low memory. Additionally, when optimized for performance, RocksDb allocates more memory for its role as the local storage provider. 

如需詳細資訊，請參閱[資源受限裝置的穩定性問題](troubleshoot.md#stability-issues-on-resource-constrained-devices)。

#### <a name="disable-unused-protocols"></a>停用未使用的通訊協定

Another way to optimize the performance of the IoT Edge hub and reduce its memory usage is to turn off the protocol heads for any protocols that you're not using in your solution. 

Protocol heads are configured by setting boolean environment variables for the IoT Edge hub module in your deployment manifests. 這三個變數分別是：

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

這三個變數都有*兩條底線*，而且可以設為 true 或 false。 

#### <a name="reduce-storage-time-for-messages"></a>減少訊息的儲存時間

The IoT Edge hub module stores messages temporarily if they cannot be delivered to IoT Hub for any reason. You can configure how long the IoT Edge hub holds on to undelivered messages before letting them expire. If you have memory concerns on your device, you can lower the **timeToLiveSecs** value in the IoT Edge hub module twin. 

timeToLiveSecs 參數的預設值是 7200 秒，也就是兩小時。 

### <a name="do-not-use-debug-versions-of-module-images"></a>請勿使用模組映像的偵錯版本

從測試情節轉移至生產環境情節時，請務必移除部署資訊清單中的偵錯設定。 檢查部署資訊清單中的模組映像是否都沒有 **\.偵錯**尾碼。 如果您已新增建立選項，以公開模組中的連接埠進行偵錯時，也請移除這些建立選項。 

## <a name="container-management"></a>容器管理

* **重要**
    * 管理您的容器登錄存取權
    * 使用標籤來管理版本

### <a name="manage-access-to-your-container-registry"></a>管理您的容器登錄存取權

將模組部署到生產 IoT Edge 裝置之前，請務必控管容器登錄的存取權，避免他人存取或變更您的容器映像。 您可以使用非公開的私人容器登錄來管理容器映像。 

在教學課程和其他說明文件中，我們會指示您在 IoT Edge 裝置使用與開發電腦上所用相同的容器登錄認證。 這些指示僅協助您更輕鬆地設定測試和開發環境，在生產情節中不應遵照這些指示。 當應用程式或服務以自動化或其他自動方式提取容器映像時，Azure Container Registry 建議[使用服務主體進行驗證](../container-registry/container-registry-auth-service-principal.md)，如同 IoT Edge 裝置一般。 為容器登錄建立具備唯讀存取權的服務主體，並且在部署資訊清單中提供該使用者名稱和密碼。

### <a name="use-tags-to-manage-versions"></a>使用標籤來管理版本

A tag is a docker concept that you can use to distinguish between versions of docker containers. 標籤是類似 **1.0** 的尾碼，位於容器存放庫的末端。 例如 **mcr.microsoft.com/azureiotedge-agent:1.0**。 標籤可以變動，而且可以隨時更改並指向另一個容器，因此當您更新模組映像時，您的團隊應同意要遵循的慣例。 

標籤也可協助您強制更新 IoT Edge 裝置。 當您將更新版模組推送至容器登錄時，請以遞增方式處理標記。 接著，將新部署和遞增的標籤一併推送到您的裝置。 容器引擎會將遞增的標籤辨識為新版本，並將最新的模組版本向下提取至您的裝置。 

如需標籤慣例的範例，請參閱[更新 IoT Edge 執行階段](how-to-update-iot-edge.md#understand-iot-edge-tags)，了解 IoT Edge 如何使用循環標籤 (rolling tag) 和特定標籤來追蹤版本。 

## <a name="networking"></a>網路功能

* **實用**
    * 檢閱輸出/輸入設定
    * Allow connections from IoT Edge devices
    * 設定 Proxy 通訊

### <a name="review-outboundinbound-configuration"></a>檢閱輸出/輸入設定

Azure IoT 中樞和 IoT Edge 之間的通訊通道一律會設定為輸出。 在大部分的 IoT Edge 情節下，必要的連線只有三個。 容器引擎必須和容納模組映像的容器登錄連線。 IoT Edge 執行階段需要和 IoT 中樞連線才能擷取裝置組態資訊，以及傳送訊息和遙測。 如果您使用自動佈建，IoT Edge 精靈必須連線至裝置佈建服務。 如需詳細資訊，請參閱[防火牆和連接埠組態規則](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)。

### <a name="allow-connections-from-iot-edge-devices"></a>Allow connections from IoT Edge devices

If your networking setup requires that you explicitly permit connections made from IoT Edge devices, review the following list of IoT Edge components:

* **IoT Edge 代理程式**可能會透過 WebSockets 持續以 AMQP/MQTT 連線方式連線到 IoT 中樞。 
* **IoT Edge 中樞**可能會透過 WebSockets 持續以 AMQP 連線或多種 MQTT 連線方式連線到 IoT 中樞。 
* **IoT Edge 精靈**對 IoT 中樞進行 HTTPS 間歇性呼叫。 

在這三個案例中，DNS 名稱會符合模式 \*.azure-devices.net。 

此外，**容器引擎**會透過 HTTPS 呼叫容器登錄。 若要擷取 IoT Edge 執行階段容器映像，DNS 名稱為 mcr.microsoft.com。 容器引擎會依部署中的設定，連線到其他登錄。 

此檢查清單是防火牆規則的起始點：

   | URL (\* = 萬用字元) | 輸出 TCP 連接埠 | 用量 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft 容器登錄 |
   | global.azure-devices-provisioning.net  | 443 | DPS 存取 (選用) |
   | \*.azurecr.io | 443 | Personal and third-party container registries |
   | \*.blob.core.windows.net | 443 | Download Azure Container Registry image deltas from blob storage  | 
   | \*.azure-devices.net | 5671、8883、443 | IoT 中樞存取 |
   | \*.docker.io  | 443 | Docker Hub access (optional) |

Some of these firewall rules are inherited from Azure Container Registry. For more information, see [Configure rules to access an Azure container registry behind a firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>設定 Proxy 通訊

如果要在使用 Proxy 伺服器的網路上部署您的裝置，裝置必須能夠透過 Proxy 通訊，以便觸達 IoT 中樞和容器登錄。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。

## <a name="solution-management"></a>解決方案管理

* **實用**
    * 設定記錄與診斷
    * 請考量測試和 CI/CD 管線

### <a name="set-up-logs-and-diagnostics"></a>設定記錄與診斷

On Linux, the IoT Edge daemon uses journals as the default logging driver. 您可以使用命令列工具 `journalctl` 查詢精靈記錄。 在 Windows 中，IoT Edge 精靈會使用 PowerShell 診斷。 使用 `Get-IoTEdgeLog` 查詢精靈記錄。 IoT Edge modules use the JSON driver for logging, which is the  default.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

正在測試 IoT Edge 部署時，通常可以存取您的裝置來擷取記錄並進行疑難排解。 在部署情節中，可能不提供該選項。 請考慮要如何收集生產環境中的裝置相關資訊。 其中一個選項是使用能夠收集其他模組資訊，並將資訊傳送至雲端的記錄模組。 其中一個記錄模組範例是 [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics)，您也可以設計自己的專用模組。 

### <a name="place-limits-on-log-size"></a>Place limits on log size

By default the Moby container engine does not set container log size limits. Over time this can lead to the device filling up with logs and running out of disk space. Consider the following options to prevent this:

**Option: Set global limits that apply to all container modules**

You can limit the size of all container logfiles in the container engine log options. The following example sets the log driver to `json-file` (recommended) with limits on size and number of files:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Add (or append) this information to a file named `daemon.json` and place it the right location for your device platform.

| 平台 | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

The container engine must be restarted for the changes to take effect.

**Option: Adjust log settings for each container module**

You can do so in the **createOptions** of each module. 例如：

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

**Additional options on Linux systems**

* Configure the container engine to send logs to `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/) by setting `journald` as the default logging driver. 

* Periodically remove old logs from your device by installing a logrotate tool. 使用下列檔案規格： 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>請考量測試和 CI/CD 管線

若要提高 IoT Edge 部署情節的效率，請考量將您的生產環境部署整合至您的測試和 CI/CD 管線。 Azure IoT Edge 支援 Azure DevOps 等多個 CI/CD 平台。 如需詳細資訊，請參閱 [Azure IoT Edge 的持續整合與持續部署](how-to-ci-cd.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [IoT Edge 自動部署](module-deployment-monitoring.md)。
* 了解 IoT Edge 如何支援[持續整合與持續部署](how-to-ci-cd.md)。
