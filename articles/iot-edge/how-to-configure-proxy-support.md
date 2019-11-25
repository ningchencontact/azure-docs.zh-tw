---
title: 針對網路 Proxy 設定裝置 - Azure IoT Edge | Microsoft Docs
description: 如何設定 Azure IoT Edge 執行階段及任何網際網路對應的 IoT Edge 模組，以透過 Proxy 伺服器進行通訊。
author: kgremban
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a4ed17ceddf01ec628d80dc3ba9f4d7ee305f3b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457165"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊

IoT Edge 裝置會傳送 HTTPS 要求以和 IoT 中樞通訊。 如果您的裝置是連線至使用 Proxy 伺服器的網路，便必須設定 IoT Edge 執行階段以透過伺服器進行通訊。 如果 IoT Edge 模組做出沒有透過 IoT Edge 中樞進行路由的 HTTP 或 HTTPS 要求，則 Proxy 伺服器也可能會影響這些個別的 IoT Edge 模組。 

This article walks through the following four steps to configure and then manage an IoT Edge device behind a proxy server: 

1. **Install the IoT Edge runtime on your device.**

   The IoT Edge installation scripts pull packages and files from the internet, so your device needs to communicate through the proxy server to make those requests. For detailed steps, see the [Install the runtime through a proxy](#install-the-runtime-through-a-proxy) section of this article. For Windows devices, the installation script also provides an [Offline installation](how-to-install-iot-edge-windows.md#offline-installation) option. 

   This step is a one-time process performed on the IoT Edge device when you first set it up. The same connections are also required when you update the IoT Edge runtime. 

2. **Configure the Docker daemon and the IoT Edge daemon on your device.**

   IoT Edge uses two daemons on the device, both of which need to make web requests through the proxy server. The IoT Edge daemon is responsible for communications with IoT Hub. The Moby daemon is responsible for container management, so communicates with container registries. For detailed steps, see the [Configure the daemons](#configure-the-daemons) section of this article. 

   This step is a one-time process performed on the IoT Edge device when you first set it up.

3. **Configure the IoT Edge agent properties in the config.yaml file on your device.**

   The IoT Edge daemon starts the edgeAgent module initially, but then the edgeAgent module is responsible for retrieving the deployment manifest from IoT Hub and starting all the other modules. For the IoT Edge agent to make the initial connection to IoT Hub, configure the edgeAgent module environment variables manually on the device itself. After the initial connection, you can configure the edgeAgent module remotely. For detailed steps, see the [Configure the IoT Edge agent](#configure-the-iot-edge-agent) section of this article.

   This step is a one-time process performed on the IoT Edge device when you first set it up.

4. **For all future module deployments, set environment variables for any module communicating through the proxy.**

   Once your IoT Edge device is set up and connected to IoT Hub through the proxy server, you need to maintain the connection in all future module deployments. For detailed steps, see the [Configure deployment manifests](#configure-deployment-manifests) section of this article. 

   This step is an ongoing process performed remotely so that every new module or deployment update maintains the device's ability to communicate through the proxy server. 

## <a name="know-your-proxy-url"></a>知道您的 Proxy URL

Before you begin any of the steps in this article, you need to know your proxy URL.

Proxy URL 採用下列格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol**是 HTTP 或 HTTPS。 The Docker daemon can use either protocol, depending on your container registry settings, but the IoT Edge daemon and runtime containers should always use HTTP to connect to the proxy.

* **proxy_host**是 Proxy 伺服器的位址。 If your proxy server requires authentication, you can provide your credentials as part of the proxy host with the following format: **user**:**password**\@**proxy_host**.

* **proxy_port**是 Proxy 回應網路流量的網路連接埠。

## <a name="install-the-runtime-through-a-proxy"></a>Install the runtime through a proxy

Whether your IoT Edge device runs on Windows or Linux, you need to access the installation packages through the proxy server. Depending on your operating system, follow the steps to install the IoT Edge runtime through a proxy server. 

### <a name="linux"></a>Linux

若您正於 Linux 裝置上安裝 IoT Edge 執行階段，請設定套件管理員以 Proxy 伺服器存取安裝套件。 例如，[設定 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy) \(英文\)。 Once your package manager is configured, follow the instructions in [Install Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md) as usual.

### <a name="windows"></a>Windows

If you're installing the IoT Edge runtime on a Windows device, you need to go through the proxy server twice. The first connection downloads the installer script file, and the second connection is during the installation to download the necessary components. You can configure proxy information in Windows settings, or include your proxy information directly in the PowerShell commands. 

The following steps demonstrate an example of a windows installation using the `-proxy` argument:

1. The Invoke-WebRequest command needs proxy information to access the installer script. Then the Deploy-IoTEdge command needs the proxy information to download the installation files. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. The Initialize-IoTEdge command doesn't need to go through the proxy server, so the second step only requires proxy information for Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

如果您的 Proxy 伺服器有複雜的認證而無法包含在 URL 中，請在 `-InvokeWebRequestParameters` 中使用 `-ProxyCredential` 參數。 例如，

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

如需 Proxy 參數的詳細資訊，請參閱 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 For more information about Windows installation options, including offline installation, see [Install Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>設定精靈

IoT Edge relies on two daemons running on the IoT Edge device. The Moby daemon makes web requests to pull container images from container registries. IoT Edge 精靈會發出 Web 要求，以和 IoT 中樞通訊。

Both the Moby and the IoT Edge daemons need to be configured to use the proxy server for ongoing device functionality. This step takes place on the IoT Edge device during initial device setup. 

### <a name="moby-daemon"></a>Moby daemon

Since Moby is built on Docker, refer to the Docker documentation to configure the Moby daemon with environment variables. 大部分的容器登錄 (包括 Docker Hub 和 Azure Container Registry) 皆支援 HTTPS 要求，因此您應該設定的參數為 **HTTPS_PROXY**。 如果您是從不支援傳輸層安全性 (TLS) 的登錄提取映像，則應該設定 **HTTP_PROXY** 參數。 

Choose the article that applies to your IoT Edge device operating system: 

* [Configure Docker daemon on Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * The Moby daemon on Linux devices keeps the name Docker.
* [Configure Docker daemon on Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * The Moby daemon on Windows devices is called iotedge-moby. The names are different because it's possible to run both Docker Desktop and Moby in parallel on a Windows device. 

### <a name="iot-edge-daemon"></a>IoT Edge 精靈

The IoT Edge daemon is configured in a similar manner to the Moby daemon. 請根據您的作業系統，使用下列相對應的步驟來針對服務設定環境變數。 

The IoT Edge daemon always uses HTTPS to send requests to IoT Hub.

#### <a name="linux"></a>Linux

在終端機中開啟編輯器以設定 IoT 精靈。 

```bash
sudo systemctl edit iotedge
```

輸入下列文字，並將 **\<proxy URL>** 取代為您 Proxy 伺服器的位址和連接埠。 然後儲存並結束。 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

重新整理服務管理員以取得適用於 IoT Edge 的新設定。

```bash
sudo systemctl daemon-reload
```

重新啟動 IoT Edge 來讓變更生效。

```bash
sudo systemctl restart iotedge
```

確認您的環境變數已經建立，並已載入新的設定。 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

以系統管理員身分開啟 PowerShell 視窗，並執行下列命令以搭配新的環境變數編輯登錄。 將 **\<proxy url>** 取代為您 Proxy 伺服器的位址和連接埠。 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

重新啟動 IoT Edge 來讓變更生效。

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>設定 IoT Edge 代理程式

IoT Edge 代理程式是在任何 IoT Edge 裝置上皆應第一個啟動的模組。 它會根據 IoT Edge config.yaml 檔案中的資訊首次啟動。 IoT Edge 代理程式接著會連線至 IoT 中樞以擷取部署資訊清單，該資訊清單會宣告有哪些其他模組應部署於裝置上。

This step takes place once on the IoT Edge device during initial device setup. 

1. 在您的 IoT Edge 裝置上開啟 config.yaml 檔案。 在 Linux 系統上，此檔案位於 **/etc/iotedge/config.yaml**。 在 Windows 系統上，此檔案位於 **C:\ProgramData\iotedge\config.yaml**。 該設定檔是受保護的，因此您需要系統管理權限以開啟它。 On Linux systems, use the `sudo` command before opening the file in your preferred text editor. On Windows, open a text editor like Notepad as administrator and then open the file. 

2. 在 config.yaml 檔案中，找到 **Edge Agent module spec** 區段。 IoT Edge 代理程式定義包含 **env** 參數，可供您於該處新增環境變數。 

3. 移除作為 env 參數之預留位置的大括號，然後將新的變數加入至新的一行上。 請記得 YAML 中的縮排為兩個空格。 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge 執行階段預設會使用 AMQP 來與 IoT 中樞通訊。 某些 Proxy 伺服器會封鎖 AMQP 連接埠。 針對那種情況，您也必須設定 edgeAgent 以使用 AMQP，而非 WebSocket。 新增第二個環境變數。

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![具環境變數的 edgeAgent 定義](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. 儲存對 config.yaml 所做的變更，然後關閉編輯器。 重新啟動 IoT Edge 來讓變更生效。 

   * Linux： 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>設定部署資訊清單  

在您的 IoT Edge 裝置已設定為能搭配您的 Proxy 伺服器運作之後，您需要在未來的部署資訊清單中繼續宣告環境變數。 You can edit deployment manifests either using the Azure portal wizard or by editing a deployment manifest JSON file. 

請一律設定兩個執行階段模組 (edgeAgent 和 edgeHub) 透過 Proxy 伺服器進行通訊，以維持與 IoT 中樞的連線。 If you remove the proxy information from the edgeAgent module, the only way to reestablish connection is by editing the config.yaml file on the device, as described in the previous section. 

Other IoT Edge modules that connect to the internet should be configured to communicate through the proxy server, too. 不過，透過 edgeHub 路由其訊息的模組，或是僅會與裝置上其他模組進行通訊的模組，則不需要 Proxy 伺服器詳細資料。 

This step is ongoing throughout the life of the IoT Edge device. 

### <a name="azure-portal"></a>Azure Portal

當您使用 [設定模組] 精靈來針對 IoT Edge 裝置建立部署時，每個模組都會有 [環境變數] 區段可供您用來設定 Proxy 伺服器連線。 

若要設定 IoT Edge 代理程式和 IoT Edge 中樞模組，請在精靈的第一個步驟中選取 [設定進階 Edge 執行階段設定]。 

![設定進階 Edge 執行階段設定](./media/how-to-configure-proxy-support/configure-runtime.png)

將 **https_proxy** 環境變數同時新增至 IoT Edge 代理程式和 IoT Edge 中樞模組定義。 如果您已在 IoT Edge 裝置上的 config.yaml 檔案中包含 **UpstreamProtocol** 環境變數，請同時將該環境變數新增至 IoT Edge 代理程式模組定義。 

![設定 https_proxy 環境變數](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

所有其他新增至部署資訊清單的模組都會遵循相同的模式。 在您設定模組名稱和映像的頁面中，會有一個環境變數區段。

### <a name="json-deployment-manifest-files"></a>JSON 部署資訊清單檔案

如果您是使用 Visual Studio Code 中的範本，或是手動建立 JSON 檔案來針對 IoT Edge 裝置建立部署，您可以將環境變數直接新增至每個模組定義中。 

使用下列 JSON 格式： 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

在包含環境變數的情況下，您的模組定義看起來應該會和下列 edgeHub 範例類似：

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

如果您已在 IoT Edge 裝置上的 confige.yaml 檔案中包含 **UpstreamProtocol** 環境變數，請同時將該環境變數新增至 IoT Edge 代理程式模組定義。 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>後續步驟

深入了解 [IoT Edge 執行階段](iot-edge-runtime.md)的角色。

使用 [Azure IoT Edge 的常見問題和解決方案](troubleshoot.md)來對安裝和設定錯誤進行疑難排解

