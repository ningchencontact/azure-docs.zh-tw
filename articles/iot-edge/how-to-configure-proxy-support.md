---
title: 針對網路 Proxy 設定裝置 - Azure IoT Edge | Microsoft Docs
description: 如何設定 Azure IoT Edge 執行階段及任何網際網路對應的 IoT Edge 模組，以透過 Proxy 伺服器進行通訊。
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 34a94a1b9c38070f5c9de22d9a9e4f24183a876c
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151013"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊

IoT Edge 裝置會傳送 HTTPS 要求以和 IoT 中樞通訊。 如果您的裝置是連線至使用 Proxy 伺服器的網路，便必須設定 IoT Edge 執行階段以透過伺服器進行通訊。 如果 IoT Edge 模組做出沒有透過 IoT Edge 中樞進行路由的 HTTP 或 HTTPS 要求，則 Proxy 伺服器也可能會影響這些個別的 IoT Edge 模組。 

這篇文章會逐步設定及管理 IoT Edge 裝置 proxy 伺服器後方的下列四個步驟： 

1. **在您的裝置上安裝 IoT Edge 執行階段。**

   IoT Edge 的安裝指令碼封裝和檔案從提取網際網路，因此您的裝置需要透過 proxy 伺服器以進行這些要求進行通訊。 如需詳細步驟，請參閱 <<c0> [ 安裝執行階段透過 proxy](#install-the-runtime-through-a-proxy)一節。 適用於 Windows 裝置，安裝指令碼也會提供[離線安裝](how-to-install-iot-edge-windows.md#offline-installation)選項。 

   這個步驟是第一次設定時，IoT Edge 裝置上執行的一次性程序。 相同的連接也是必要的當您更新 IoT Edge 執行階段。 

2. **在裝置上設定 Docker 精靈和 IoT Edge 精靈。**

   IoT Edge 會使用兩個精靈，在裝置上，這兩者都必須先通過 proxy 伺服器的 web 要求。 IoT Edge 服務精靈會負責使用 IoT 中樞通訊。 白鯨精靈會負責管理容器，因此會與容器登錄庫進行通訊。 如需詳細步驟，請參閱 <<c0> [ 設定精靈](#configure-the-daemons)一節。 

   這個步驟是第一次設定時，IoT Edge 裝置上執行的一次性程序。

3. **在您的裝置上 config.yaml 檔案中設定的 IoT Edge 代理程式屬性。**

   IoT Edge 精靈啟動 edgeAgent 模組一開始，但 edgeAgent 模組則是負責從 IoT 中樞擷取部署資訊清單，並啟動所有其他的模組。 IoT Edge 代理程式才會建立初始連接到 IoT 中樞，edgeAgent 模組的環境變數以手動方式在裝置上設定本身。 初始連線之後，您可以從遠端設定 edgeAgent 模組。 如需詳細步驟，請參閱 < [IoT Edge 代理程式設定](#configure-the-iot-edge-agent)一節。

   這個步驟是第一次設定時，IoT Edge 裝置上執行的一次性程序。

4. **對於所有未來的模組部署，設定環境變數，透過 proxy 進行通訊的任何模組。**

   一旦您的 IoT Edge 裝置已設定，並透過 proxy 伺服器連線到 IoT 中樞，您需要維護所有未來的模組部署中的連接。 如需詳細步驟，請參閱 <<c0> [ 設定部署資訊清單](#configure-deployment-manifests)一節。 

   這個步驟是持續進行的遠端執行，讓每個新的模組或部署更新會維護裝置的能力，透過 proxy 伺服器進行通訊。 

## <a name="know-your-proxy-url"></a>知道您的 Proxy URL

開始進行任何這篇文章中的步驟之前，您需要知道您的 proxy URL。

Proxy URL 採用下列格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol**是 HTTP 或 HTTPS。 Docker 精靈可以使用任一通訊協定，根據您的容器登錄設定，但 「 IoT Edge 服務精靈 」 和 「 執行階段容器應該一律使用 HTTP 來連線到 proxy。

* **proxy_host**是 Proxy 伺服器的位址。 如果您的 proxy 伺服器需要驗證，您可以使用下列格式的 proxy 主機一部分提供您的認證：**使用者**:**密碼**\@**proxy_host**.

* **proxy_port**是 Proxy 回應網路流量的網路連接埠。

## <a name="install-the-runtime-through-a-proxy"></a>安裝執行階段透過 proxy

是否要在 Windows 或 Linux 上，執行您的 IoT Edge 裝置，您需要透過 proxy 伺服器存取安裝套件。 根據您的作業系統，請依照下列步驟來安裝 IoT Edge 執行階段，透過 proxy 伺服器。 

### <a name="linux"></a>Linux

若您正於 Linux 裝置上安裝 IoT Edge 執行階段，請設定套件管理員以 Proxy 伺服器存取安裝套件。 例如，[設定 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy) \(英文\)。 設定您的套件管理員之後，請依照[在 Linux (ARM32v7/armhf) 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux-arm.md)或[在 Linux (x64) 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)中的指示執行。

### <a name="windows"></a>Windows

如果您要在 Windows 裝置上安裝 IoT Edge 執行階段，您需要兩次通過 proxy 伺服器。 第一個連線下載安裝程式指令碼檔案，而且第二個連接只下載必要元件安裝。 您可以在 [Windows 設定] 中設定 proxy 資訊，或直接在 PowerShell 命令中包含您的 proxy 資訊。 

下列步驟示範如何使用 windows 安裝的範例`-proxy`引數：

1. Invoke-webrequest 命令需要存取安裝程式指令碼的 proxy 資訊。 然後部署 IoTEdge 命令必須以下載安裝檔案的 proxy 資訊。 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. 初始化 IoTEdge 命令不需要通過 proxy 伺服器，因此第二個步驟只需要 Invoke-webrequest proxy 資訊。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

如需 Proxy 參數的詳細資訊，請參閱 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 如需有關 Windows 安裝選項，包括離線安裝，請參閱[在 Windows 上的安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-windows.md)。

## <a name="configure-the-daemons"></a>設定精靈

IoT Edge 會依賴兩個 IoT Edge 裝置上執行的精靈。 從容器登錄庫，白鯨精靈會建立 web 要求以提取容器映像。 IoT Edge 精靈會發出 Web 要求，以和 IoT 中樞通訊。

白鯨 」 和 「 IoT Edge 精靈 」 都必須設定為使用 proxy 伺服器進行持續的裝置功能。 這個步驟會在初始裝置安裝期間需要 IoT Edge 裝置上的位置。 

### <a name="moby-daemon"></a>白鯨精靈

因為白鯨的以 Docker 為基礎，請參閱 Docker 文件，若要使用環境變數設定白鯨精靈。 大部分的容器登錄 (包括 Docker Hub 和 Azure Container Registry) 皆支援 HTTPS 要求，因此您應該設定的參數為 **HTTPS_PROXY**。 如果您是從不支援傳輸層安全性 (TLS) 的登錄提取映像，則應該設定 **HTTP_PROXY** 參數。 

選擇適用於您的 IoT Edge 裝置作業系統的發行項： 

* [在 Linux 上設定 Docker 精靈](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * 在 Linux 上的裝置上的白鯨服務精靈會保留 Docker 的名稱。
* [Windows 上設定 Docker 精靈](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * 在 Windows 裝置上的白鯨服務精靈會呼叫 iotedge 白鯨。 名稱彼此不同，因為它是可以在 Windows 裝置上執行 Docker Desktop 」 和 「 白鯨 」 都以平行方式。 

### <a name="iot-edge-daemon"></a>IoT Edge 精靈

IoT Edge 服務精靈已在白鯨背景類似的方式。 請根據您的作業系統，使用下列相對應的步驟來針對服務設定環境變數。 

IoT Edge 背景一律使用 HTTPS，將要求傳送至 IoT 中樞。

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

這個步驟會在初始裝置安裝期間需要 IoT Edge 裝置上一次。 

1. 在您的 IoT Edge 裝置上開啟 config.yaml 檔案。 在 Linux 系統上，此檔案位於 **/etc/iotedge/config.yaml**。 在 Windows 系統上，此檔案位於 **C:\ProgramData\iotedge\config.yaml**。 該設定檔是受保護的，因此您需要系統管理權限以開啟它。 在 Linux 系統上使用`sudo`命令之前，先在您慣用的文字編輯器中開啟檔案。 在 Windows 中，開啟 [記事本] 之類的文字編輯器，以系統管理員身分，然後開啟檔案。 

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

在您的 IoT Edge 裝置已設定為能搭配您的 Proxy 伺服器運作之後，您需要在未來的部署資訊清單中繼續宣告環境變數。 您可以編輯部署資訊清單使用 Azure 入口網站精靈，或藉由編輯部署資訊清單 JSON 檔案。 

請一律設定兩個執行階段模組 (edgeAgent 和 edgeHub) 透過 Proxy 伺服器進行通訊，以維持與 IoT 中樞的連線。 如果移除 edgeAgent 模組中的 proxy 資訊，請重新建立連線的唯一方式是藉由編輯 config.yaml 裝置上的檔案上, 一節中所述。 

連線到網際網路的其他 IoT Edge 模組應該設定為太透過 proxy 伺服器，進行通訊。 不過，透過 edgeHub 路由其訊息的模組，或是僅會與裝置上其他模組進行通訊的模組，則不需要 Proxy 伺服器詳細資料。 

這個步驟是進行中的 IoT Edge 裝置生命週期。 

### <a name="azure-portal"></a>Azure 入口網站

當您使用 [設定模組]  精靈來針對 IoT Edge 裝置建立部署時，每個模組都會有 [環境變數]  區段可供您用來設定 Proxy 伺服器連線。 

若要設定 IoT Edge 代理程式和 IoT Edge 中樞模組，請在精靈的第一個步驟中選取 [設定進階 Edge 執行階段設定]  。 

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

