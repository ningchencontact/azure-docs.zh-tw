---
title: "疑難排解 Azure IoT 邊緣 |Microsoft 文件"
description: "解決一般問題和了解 Azure IoT 邊緣的疑難排解技術"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f61f0bf8234e747ae38146d1a5ea030e3163fa3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2017
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>常見問題和解決方式的 Azure IoT 邊緣

如果您遇到您環境中執行 Azure IoT 邊緣的問題，使用做為指南這篇文章的疑難排解和解決方式。 

## <a name="standard-diagnostic-steps"></a>標準的診斷步驟 

當您遇到問題時，深入了解 IoT 邊緣裝置的狀態檢閱容器的記錄檔與所傳遞的訊息與裝置。 這一節中使用的命令與工具來收集資訊。 

* 查看 docker 容器來偵測問題的記錄檔。 開始使用您已部署的容器，然後查看構成 IoT 邊緣執行階段的容器： 邊緣代理程式和邊緣中樞。 邊緣代理程式記錄檔通常會提供在每個容器的生命週期的資訊。 邊緣中樞記錄檔提供傳訊和路由的資訊。 

   ```cmd
   docker logs <container name>
   ```

* 檢視通過邊緣集線器的訊息和裝置屬性更新的詳細資訊記錄檔收集 insights 從執行階段容器。

   ```cmd
   iotedgectl setup --runtime-log-level DEBUG
   ```

* 如果您遇到連線問題，請檢查您的邊緣裝置環境變數，例如您的裝置連接字串：

   ```cmd
   docker exec edgeAgent printenv
   ```

您也可以檢查 IoT 中樞與邊緣 IoT 裝置之間傳送的訊息。 使用檢視這些訊息[Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) Visual Studio Code 擴充功能。 如需詳細指引，請參閱[便利的工具，當您開發與 Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)。

之後調查記錄檔和資訊的訊息中，您也可以嘗試重新啟動 Azure IoT 邊緣執行階段：

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>邊緣代理程式後，停止約一分鐘

邊緣代理程式啟動並執行成功約一分鐘，然後停止。 記錄檔表示邊緣代理程式正在嘗試透過 AMQP 連線到 IoT 中樞，則大約 30 秒之後嘗試使用 AMQP 透過 websocket 連線。 時失敗，就會結束邊緣代理程式。 

範例邊緣代理程式記錄檔：

```
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>根本原因
主機網路的網路設定會使得邊緣代理程式無法接近網路。 代理程式將嘗試透過 AMQP （連接埠 5671） 連接的第一次。 如果這個作業失敗，它會嘗試 websockets （連接埠 443）。

IoT 邊緣執行階段會設定每個模組的網路上通訊。 On Linux，此網路是網路橋接器。 在 Windows 中，它會使用 nat。 這個問題是較常見的 Windows 裝置使用 Windows 容器使用 NAT 網路上。 

### <a name="resolution"></a>解決方案
確定網際網路的 IP 位址指派給此橋接器/NAT 網路的路由。 有時在主機上的 VPN 組態會覆寫 IoT 邊緣網路。 

## <a name="edge-hub-fails-to-start"></a>邊緣中樞無法啟動

邊緣中樞無法啟動，並列印至記錄檔的下列訊息： 

```
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>根本原因
在主機上的某些其他處理序已經繫結連接埠 443。 邊緣中樞對應連接埠 5671 與在閘道案例中使用 443。 如果另一個處理序已經繫結此連接埠，此連接埠對應將會失敗。 

### <a name="resolution"></a>解決方案
尋找並停止處理序正在使用連接埠 443。 此程序通常是網頁伺服器。

## <a name="edge-agent-cant-access-a-modules-image-403"></a>邊緣代理程式無法存取模組的映像 (403)
容器無法執行，而且邊緣代理程式記錄檔顯示 403 錯誤。 

### <a name="root-cause"></a>根本原因
邊緣代理程式沒有存取模組的映像的權限。 

### <a name="resolution"></a>解決方案
請嘗試執行`iotedgectl login`命令一次。

## <a name="next-steps"></a>後續步驟
您認為您在 IoT 邊緣平台找到 bug？ 請[提出議題](https://github.com/Azure/iot-edge/issues)，讓我們可以持續改善。 
