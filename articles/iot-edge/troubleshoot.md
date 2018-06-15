---
title: 對 Azure IoT Edge 進行移難排解 | Microsoft Docs
description: 解決 Azure IoT Edge 的常見問題及了解其疑難排解技術
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad22b0cd1457c1d4146a75047ff18e916c0c7ccd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633531"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常見問題和解決方案

如果您在您的環境中執行 Azure IoT Edge 時遇到問題，請使用本文作為疑難排解和解決方案的指引。 

## <a name="standard-diagnostic-steps"></a>標準的診斷步驟 

當您遇到問題時，可透過檢閱容器記錄與裝置的往來訊息，深入了解 IoT Edge 裝置的狀態。 可使用這一節中的命令與工具來收集資訊。 

* 查看 Docker 容器的記錄來偵測問題。 從您已部署的容器開始著手，然後查看構成 IoT Edge 執行階段的容器：IoT 代理程式和 IoT 中樞。 IoT 代理程式記錄通常會提供每個容器的生命週期相關資訊。 IoT 中樞記錄會提供傳訊和路由的相關資訊。 

   ```cmd
   docker logs <container name>
   ```

* 檢視通過 Edge 中樞的訊息，以及透過執行階段容器的詳細記錄來收集裝置屬性更新的見解。

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```
   
* 從 iotedgectl 命令檢視詳細資訊記錄：

   ```cmd
   iotedgectl --verbose DEBUG <command>
   ```

* 如果您遇到連線問題，請檢查您的邊緣裝置環境變數，例如您的裝置連接字串：

   ```cmd
   docker exec edgeAgent printenv
   ```

您也可以檢查在 IoT 中樞與 IoT Edge 裝置之間傳送的訊息。 您可以使用 Visual Studio Code 的 [Azure IoT 工具組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)擴充功能來檢視這些訊息。 如需詳細指引，請參閱[使用 Azure IoT 進行開發時的便利工具](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)。

檢查過記錄和訊息並取得資訊後，您也可以嘗試重新啟動 Azure IoT Edge 執行階段：

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Edge 代理程式會在大約一分鐘後停止

Edge 代理程式會啟動並成功執行約一分鐘，然後停止。 記錄指出 Edge 代理程式會嘗試透過 AMQP 連線到 IoT 中樞，然後大約 30 秒之後嘗試使用 AMQP 透過 WebSocket 連線。 當這些作業失敗後，Edge 代理程式就會退出。 

Edge 代理程式記錄的範例：

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>根本原因
主機網路上的網路組態阻止了 Edge 代理程式觸達該網路。 代理程式首先嘗試透過 AMQP (連接埠 5671) 進行連線。 如果此作業失敗，代理程式會嘗試 Websocket (連接埠 443)。

IoT Edge 執行階段會為每個模組設定要在其中通訊的網路。 在 Linux 上，此網路是橋接網路。 在 Windows 上則是使用 NAT。 此問題較常見於使用 Windows 容器 (使用 NAT 網路) 的 Windows 裝置。 

### <a name="resolution"></a>解決方案
確認指派給此橋接器/NAT 網路的 IP 位址能路由至網際網路。 有時候主機上的 VPN 組態會覆寫 IoT Edge 網路。 

## <a name="edge-hub-fails-to-start"></a>Edge 中樞無法啟動

Edge 中樞無法啟動，且將下列訊息輸出至記錄： 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>根本原因
在主機機器上的某些其他程序已繫結連接埠 443。 Edge 中樞會對應連接埠 5671 和 443，以在閘道案例中使用。 如果另一個程序已繫結此連接埠，則此連接埠對應會失敗。 

### <a name="resolution"></a>解決方案
尋找並停止正在使用連接埠 443 的程序。 此程序通常是網頁伺服器。

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Edge 代理程式無法存取模組的映像 (403)
容器無法執行，而且 Edge 代理程式記錄顯示 403 錯誤。 

### <a name="root-cause"></a>根本原因
Edge 代理程式沒有存取模組映像的權限。 

### <a name="resolution"></a>解決方案
再次嘗試執行 `iotedgectl login` 命令。

## <a name="iotedgectl-cant-find-docker"></a>iotedgectl 找不到 Docker

命令 `iotedgectl setup` 或 `iotedgectl start` 失敗，並將下列訊息列印到記錄檔：
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>根本原因
iotedgectl 找不到 Docker，但這是必要項目。

### <a name="resolution"></a>解決方案
安裝 Docker，確定它正在執行，然後重試。

## <a name="iotedgectl-setup-fails-with-an-invalid-hostname"></a>主機名稱無效，iotedgectl 設定失敗

命令 `iotedgectl setup` 失敗，並列印下列訊息： 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>根本原因
IoT Edge 執行階段只能支援少於 64 個字元的主機名稱。 如此通常不會造成實體機器發生問題，但當在虛擬機器上設定執行階段時，就可能會產生問題。 在 Azure 中代管的 Windows 虛擬機器自動產生的主機名稱通常很長。 

### <a name="resolution"></a>解決方案
當發現這個錯誤時，可以設定虛擬機器的 DNS 名稱，然後將 DNS 名稱設定為安裝命令中的主機名稱來解決。

1. 在 Azure 入口網站中，瀏覽至虛擬機器的概觀頁面。 
2. 選取 DNS 名稱下的 [設定]。 如果虛擬機器已設定 DNS 名稱，則不需要設定新的名稱。 

   ![設定 DNS 名稱](./media/troubleshoot/configure-dns.png)

3. 提供 **DNS 名稱標籤**的值，並選取 [儲存]。
4. 複製新的 DNS 名稱，格式應該是 **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**。
5. 在虛擬機器中，使用下列命令以您的 DNS 名稱設定 IoT Edge 執行階段：

   ```input
   iotedgectl setup --connection-string "<connection string>" --nopass --edge-hostname "<DNS name>"
   ```

## <a name="next-steps"></a>後續步驟
您在 IoT Edge 平台中發現到錯誤嗎？ 請[提交問題](https://github.com/Azure/iot-edge/issues)，讓我們可以持續進行改善。 
