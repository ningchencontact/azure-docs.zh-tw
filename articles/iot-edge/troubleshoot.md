---
title: 移難排解 - Azure IoT Edge | Microsoft Docs
description: 使用本文以了解 Azure IoT Edge 的標準診斷技巧，例如擷取元件狀態和記錄，以及解決常見的問題
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 00147002317f15345f01c88e81973837d16e6669
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797617"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常見問題和解決方案

如果您在您的環境中執行 Azure IoT Edge 時遇到問題，請使用本文作為疑難排解和解決方案的指引。

## <a name="run-the-iotedge-check-command"></a>執行 [檢查] 命令 iotedge

第一個步驟進行疑難排解的 IoT Edge 時應該使用`check`命令，針對常見的問題便會執行回收的組態與連線能力測試。 `check`命令適用於[釋放 version:1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7)和更新版本。

您可以執行`check`命令，如下所示，或包含`--help`旗標來查看完整的選項清單：

* 在 Linux 上：

  ```bash
  sudo iotedge check
  ```

* 在 Windows 上：

  ```powershell
  iotedge check
  ```

執行工具所檢查的類型可以分類為：

* 設定檢查：檢查可防止 Edge 裝置連接到雲端，包括問題的詳細資料*config.yaml*和 container engine。
* 連線檢查：確認 IoT Edge 執行階段可以存取主應用程式在裝置上的連接埠，而且所有 IoT Edge 的元件可以都連接到 IoT 中樞。
* 實際執行整備檢查：會尋找建議實際執行最佳做法，例如裝置憑證授權單位 (CA) 憑證和模組記錄檔設定的狀態。

診斷檢查完整清單，請參閱 <<c0> [ 內建的疑難排解功能](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)。

## <a name="standard-diagnostic-steps"></a>標準的診斷步驟

如果您遇到問題，您可以深入了解您的 IoT Edge 裝置的狀態藉由檢視容器記錄檔和訊息傳遞與裝置。 可使用這一節中的命令與工具來收集資訊。

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>檢查 「 IoT Edge 安全性管理員 」 和其記錄檔的狀態

在 Linux 上：
- 若要檢視 IoT Edge 安全性管理員的狀態：

   ```bash
   sudo systemctl status iotedge
   ```

- 若要檢視 IoT Edge 安全性管理員的記錄：

    ```bash
    sudo journalctl -u iotedge -f
    ```

- 若要檢視 IoT Edge 安全性管理員更詳細的記錄：

   - 編輯 iotedge 精靈設定：

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - 更新以下幾行：
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - 重新啟動 IoT Edge 安全性精靈：
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

在 Windows 上：
- 若要檢視 IoT Edge 安全性管理員的狀態：

   ```powershell
   Get-Service iotedge
   ```

- 若要檢視 IoT Edge 安全性管理員的記錄：

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>如果 IoT Edge 安全性管理員未執行，請驗證您的 yaml 組態檔

> [!WARNING]
> YAML 檔案不能包含索引標籤，為縮排。 請改用 2 個空格。

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>檢查容器記錄的問題

在 IoT Edge 安全性精靈開始執行後，請查看容器的記錄以偵測問題。 先查看你的已部署容器，然后查看构成 IoT Edge 运行时的容器：edgeAgent 和 edgeHub。 IoT Edge 代理日志通常提供有关每个容器的生命周期的信息。 IoT Edge 中心日志提供有关消息传送和路由的信息。 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>查看通过 IoT Edge 中心的消息

查看通过 IoT Edge 中心的消息，并通过来自运行时容器的详细日志收集见解。 若要開啟這些容器上的詳細資訊記錄，請在 yaml 組態檔中設定 `RuntimeLogLevel`。 若要開啟檔案：

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

根據預設，`agent` 元素看起來如下範例所示：

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

將 `env: {}` 取代為：

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML 檔案不可包含使用 Tab 鍵的縮排。 請改用 2 個空格。

儲存檔案並重新啟動 IoT Edge 安全性管理員。

您也可以檢查在 IoT 中樞與 IoT Edge 裝置之間傳送的訊息。 請使用適用於 Visual Studio Code 的 [Azure IoT 中樞工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)延伸模組 (先前稱為「Azure IoT 工作組」延伸模組) 來檢視這些訊息。 如需詳細資訊，請參閱[使用 Azure IoT 進行開發時的便利工具](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)。

### <a name="restart-containers"></a>重新啟動容器
檢查過記錄和訊息並取得資訊後，您可以嘗試重新啟動容器：

```
iotedge restart <container name>
```

重新啟動 IoT Edge 執行階段容器：

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>重新啟動 IoT Edge 安全性管理員

如果問題仍持續發生，您可以嘗試重新啟動 IoT Edge 安全性管理員。

在 Linux 上：

   ```cmd
   sudo systemctl restart iotedge
   ```

在 Windows 上：

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge 代理在大约一分钟后停止

edgeAgent 模块将启动并成功运行大约一分钟，然后停止。 日志表明，IoT Edge 代理尝试通过 AMQP 连接到 IoT 中心，并且尝试使用 AMQP 通过 WebSocket 进行连接。 该操作失败时，IoT Edge 代理将会退出。 

示例 edgeAgent 日志：

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>根本原因
主机网络上的某个网络配置阻止 IoT Edge 代理到达该网络。 代理程式首先嘗試透過 AMQP (連接埠 5671) 進行連線。 如果連線失敗，代理程式會嘗試 Websocket (連接埠 443)。

IoT Edge 執行階段會為每個模組設定要在其中通訊的網路。 在 Linux 上，此網路是橋接網路。 在 Windows 上則是使用 NAT。 此問題較常見於使用 Windows 容器 (使用 NAT 網路) 的 Windows 裝置。 

### <a name="resolution"></a>解決方案
確認指派給此橋接器/NAT 網路的 IP 位址能路由至網際網路。 有時候主機上的 VPN 組態會覆寫 IoT Edge 網路。 

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge 中心未能启动

edgeHub 模块未能启动，并且向日志输出了以下消息： 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>根本原因
在主機機器上的某些其他程序已繫結連接埠 443。 IoT Edge 中心映射端口 5671 和 443 以在网关方案中使用。 如果另一個程序已繫結此連接埠，則此連接埠對應會失敗。 

### <a name="resolution"></a>解決方案
尋找並停止正在使用連接埠 443 的程序。 此程序通常是網頁伺服器。

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge 代理无法访问某个模块的映像 (403)
某个容器未能运行，并且 edgeAgent 日志显示了 403 错误。 

### <a name="root-cause"></a>根本原因
Iot Edge 代理无权访问某个模块的映像。 

### <a name="resolution"></a>解決方案
確定您已在部署資訊清單中正確指定登錄認證

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge 安全性精靈因主機名稱無效而失敗

命令 `sudo journalctl -u iotedge` 失敗，並列印下列訊息： 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>根本原因
IoT Edge 執行階段只能支援少於 64 個字元的主機名稱。 實體機器通常不需要很長的主機名稱，但問題在虛擬機器上更常發生。 在 Azure 中代管的 Windows 虛擬機器自動產生的主機名稱通常很長。 

### <a name="resolution"></a>解決方案
當發現這個錯誤時，可以設定虛擬機器的 DNS 名稱，然後將 DNS 名稱設定為安裝命令中的主機名稱來解決。

1. 在 Azure 入口網站中，瀏覽至虛擬機器的概觀頁面。 
2. 選取 DNS 名稱下的 [設定]。 如果虛擬機器已設定 DNS 名稱，則不需要設定新的名稱。 

   ![設定虛擬機器的 DNS 名稱](./media/troubleshoot/configure-dns.png)

3. 提供 **DNS 名稱標籤**的值，並選取 [儲存]。
4. 複製新的 DNS 名稱，格式應該是 **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**。
5. 在虛擬機器中，使用下列命令以您的 DNS 名稱設定 IoT Edge 執行階段：

   - 在 Linux 上：

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - 在 Windows 上：

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>資源受限裝置的穩定性問題 
您可能會遇到受限裝置 (例如 Raspberry Pi) 的穩定性問題，尤其在該裝置當作閘道的時候。 徵兆包括 Edge 中樞模組的記憶體不足例外狀況，下游裝置無法連線，或裝置在幾小時後停止傳送遙測訊息。

### <a name="root-cause"></a>根本原因
IoT Edge 中心是 IoT Edge 运行时的一部分，默认情况下已针对性能进行了优化，并尝试分配大块内存。 此最佳化不適合用於受限邊緣裝置，而且可能會造成穩定性問題。

### <a name="resolution"></a>解決方案
对于 IoT Edge 中心，请将环境变量 **OptimizeForPerformance** 设置为 **false**。 作法有二：

在 UI 中： 

在门户中，导航到“设备详细信息” > “设置模块” > “配置高级 Edge 运行时设置”。 为 Edge 中心模块创建名为 *OptimizeForPerformance*、设置为 *false* 的环境变量。

![將 OptimizeForPerformance 設定為 false](./media/troubleshoot/optimizeforperformance-false.png)

**或**

在部署資訊清單中：

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>無法在 Windows 上取得 IoT Edge 精靈記錄 (daemon log)
如果您在 Windows 上使用 `Get-WinEvent` 時收到 EventLogException，請檢查您的登錄項目。

### <a name="root-cause"></a>根本原因
`Get-WinEvent` PowerShell 命令須依賴存在的登錄項目，才能根據特定 `ProviderName` 尋找記錄。

### <a name="resolution"></a>解決方案
設定 IoT Edge 精靈的登錄項目。 建立具有下列內容的 **iotedge.reg** 檔案，然後按兩下此檔案或使用 `reg import iotedge.reg` 命令將檔案匯入至 Windows 登錄：

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge 模組因發生 404 錯誤而無法將訊息傳送給 edgeHub

自訂 IoT Edge 模組因發生 404 `Module not found` 錯誤而無法將訊息傳送給 edgeHub。 IoT Edge 精靈會將下列訊息輸出至記錄： 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

### <a name="root-cause"></a>根本原因
基於安全考量，IoT Edge 精靈會針對連線至 edgeHub 的所有模組強制執行處理序識別。 它會確認模組傳送的所有訊息都來自該模組的主要處理序識別碼。 如果傳送訊息之模組的來源處理序識別碼與最初確立的識別碼不同，它就會發出 404 錯誤訊息來拒絕該訊息。

### <a name="resolution"></a>解決方案
請確定自訂 IoT Edge 模組一律使用相同的處理序識別碼將訊息傳送給 edgeHub。 比方說，請務必`ENTRYPOINT`而非`CMD`命令，是在 Docker 檔案中，因為`CMD`將會導致其中一個處理序模組的識別碼和 bash 命令執行的主要程式，而另一個處理序識別碼`ENTRYPOINT`會導致單一處理序識別碼。


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>適用於 IoT Edge 部署的防火牆和連接埠設定規則
Azure IoT Edge 允许使用支持的 IoT 中心协议从本地服务器来与 Azure 云通信，具体请参阅[选择通信协议](../iot-hub/iot-hub-devguide-protocols.md)。 為了加強安全性，Azure IoT Edge 和 Azure IoT 中樞之間的通訊通道一律會設定為輸出。 此設定根據[服務輔助通訊模式](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)，可有效減少惡意實體要探索的攻擊面。 僅當 Azure IoT 中樞需要將訊息推送到 Azure IoT Edge 裝置的特定案例時，才需要輸入通信。 使用安全的 TLS 通道保護雲端到裝置訊息，並且可以使用 X.509 憑證和 TPM 裝置模組進一步保護雲端。 「Azure IoT Edge 安全性管理員」會控管此通訊的建立方式，請參閱 [IoT Edge 安全性管理員](../iot-edge/iot-edge-security-manager.md)。

雖然 IoT Edge 提供增強的設定來保護 Azure IoT Edge 執行階段和已部署的模組，但它仍然倚賴基礎的機器和網路設定。 因此，必须确保设置适当的网络和防火墙规则来保护 Edge 与云之间的通信。 为托管 Azure IoT Edge 运行时的底层服务器配置防火墙规则时，可参考下表中的指导：

|Protocol|Port|傳入|傳出|指引|
|--|--|--|--|--|
|MQTT|8883|已封鎖 (預設值)|已封鎖 (預設值)|<ul> <li>使用 MQTT 作為通訊協定時，請將「傳出」(輸出) 設定為「開放」。<li>IoT Edge 不支援適用於 MQTT 的 1883。 <li>應該將傳入 (輸入) 連線封鎖。</ul>|
|AMQP|5671|已封鎖 (預設值)|開放 (預設值)|<ul> <li>IoT Edge 的預設通訊協定。 <li> 如果未設定 Azure IoT Edge 使用其他支援的通訊協定，或 AMQP 是所需的通訊協定，則必須設定為「開放」。<li>IoT Edge 不支援適用於 AMQP 的 5672。<li>當 Azure IoT Edge 使用不同的 IoT Hub 已支援通訊協定時，請封鎖此連接埠。<li>應該將傳入 (輸入) 連線封鎖。</ul></ul>|
|HTTPS|443|已封鎖 (預設值)|開放 (預設值)|<ul> <li>設定連出 (輸出) 在 443 上開啟以進行 IoT Edge 佈建。 使用手動指令碼或 Azure IoT 裝置佈建服務 (DPS) 時，就需要此設定。 <li>「傳入」(輸入) 連線應該只有針對特定案例才設定為「開放」： <ul> <li>  如果您有透明閘道，而此閘道具有可能傳送方法要求的分葉裝置。 在此情況下，無須對外部網路開放連接埠 443，即可連線至 IoTHub 或透過 Azure IoT Edge 提供 IoTHub 服務。 因此，可將傳入規則限制成只從內部網路開放「傳入」(輸入)。 <li> 針對「用戶端到裝置」(C2D) 案例。</ul><li>IoT Edge 不支援適用於 HTTP 的 80。<li>如果無法在企業中設定非 HTTP 通訊協定 (例如 AMQP 或 MQTT)；則可透過 WebSocket 傳送訊息。 在該情況下，會使用連接埠 443 來進行 WebSocket 通訊。</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge 代理模块持续报告“配置文件为空”，且设备上不会启动任何模块

设备在启动部署中定义的模块时出现问题。 只有 edgeAgent 在运行，但它持续报告“配置文件为空...”。

### <a name="potential-root-cause"></a>潜在的根本原因
默认情况下，IoT Edge 在模块自身的隔离容器网络中启动模块。 在此专用网络中，设备可能会遇到 DNS 名称解析方面的问题。

### <a name="resolution"></a>解決方案

**選項 1：在容器引擎设置中设置 DNS 服务器**

在要应用到引擎启动的所有容器模块的容器引擎设置中，指定环境的 DNS 服务器。 创建名为 `daemon.json` 的文件，并在其中指定要使用的 DNS 服务器。 例如：

```
{
    "dns": ["1.1.1.1"]
}
```

上面的示例将 DNS 服务器设置为可公开访问的 DNS 服务。 如果 Edge 设备无法从其所在环境访问此 IP，请将其替换为可访问的 DNS 服务器地址。

将 `daemon.json` 放入平台上的适当位置： 

| 平台 | 位置 |
| --------- | -------- |
|  Linux | `/etc/docker` |
| 包含 Windows 容器的 Windows 主机 | `C:\ProgramData\iotedge-moby\config` |

如果该位置已包含 `daemon.json` 文件，请在其中添加 **dns** 密钥，然后保存该文件。

*重启容器引擎，使更新生效*

| 平台 | 命令 |
| --------- | -------- |
|  Linux | `sudo systemctl restart docker` |
| Windows（管理 Powershell） | `Restart-Service iotedge-moby -Force` |

**選項 2：在每个模块的 IoT Edge 部署中设置 DNS 服务器**

可以针对 IoT Edge 部署中每个模块的 *createOptions* 设置 DNS 服务器。 例如：

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

同时，请务必为 *edgeAgent* 和 *edgeHub* 模块设置此项。 

## <a name="next-steps"></a>後續步驟
您在 IoT Edge 平台中發現到錯誤嗎？ [提交問題](https://github.com/Azure/iotedge/issues)，讓我們可以持續進行改善。 

如果您有其他問題，請建立[支援要求](https://portal.azure.com/#create/Microsoft.Support)以取得協助。 

