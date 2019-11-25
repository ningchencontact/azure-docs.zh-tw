---
title: 了解執行階段如何管理裝置 - Azure IoT Edge | Microsoft Docs
description: Learn how the IoT Edge runtime manages modules, security, communication, and reporting on your devices
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6382159c2a9d73b9db21dd0467be0e68cf4b4c2d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456604"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 執行階段和架構

The IoT Edge runtime is a collection of programs that turn a device into an IoT Edge device. Collectively, the IoT Edge runtime components enable IoT Edge devices to receive code to run at the edge and communicate the results. 

The IoT Edge runtime is responsible for the following functions on IoT Edge devices:

* 在裝置上安裝和更新工作負載。
* 在裝置上維護 Azure IoT Edge 安全性標準。
* Ensure that [IoT Edge modules](iot-edge-modules.md) are always running.
* 將模組健康情況報告至雲端，以便進行遠端監控。
* Manage communication between downstream devices and IoT Edge devices.
* Manage communication between modules on the IoT Edge device.
* Manage communication between the IoT Edge device and the cloud.

![執行階段會將深入見解和模組健康情況傳達到 IoT 中樞](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 執行階段的責任分為兩類：通訊和模組管理。 These two roles are performed by two components that are part of the IoT Edge runtime. The *IoT Edge hub* is responsible for communication, while the *IoT Edge agent* deploys and monitors the modules. 

IoT Edge 中樞和 IoT Edge 代理程式都是模組，就像 IoT Edge 裝置上執行的任何其他模組。 They're sometimes referred to as the *runtime modules*. 

## <a name="iot-edge-hub"></a>IoT Edge 中樞

IoT Edge 中樞是組成 Azure IoT Edge 執行階段的兩個模組之一。 它藉由公開與 IoT 中樞相同的通訊協定端點來作為 IoT 中樞的本機 Proxy。 此一致性表示用戶端 (不論是裝置或模組) 都可連線到 IoT Edge 執行階段，就像它們對 IoT 中樞所做的。 

>[!NOTE]
> IoT Edge hub supports clients that connect using MQTT or AMQP. 並不支援使用 HTTP 的用戶端。 

IoT Edge 中樞不是在本機執行的 IoT 中樞完整版本。 IoT Edge 中樞會以無訊息方式委派幾件事給 IoT 中樞。 例如，當裝置第一次嘗試連線時，IoT Edge 中樞會將驗證要求轉送到 IoT 中樞。 建立第一個連線之後，IoT Edge 中樞就會在本機快取安全性資訊。 允許後續來自該裝置的連線，而不需向雲端驗證。 

為了降低 IoT Edge 解決方案所使用的頻寬，IoT Edge 中樞會將要連至雲端的連線數目最佳化。 IoT Edge hub takes logical connections from clients like modules or downstream devices and combines them for a single physical connection to the cloud. 此程序的詳細資料對解決方案的其餘部分而言是透明的。 用戶端認為它們有自己的連線可連至雲端，即使它們全部都會透過相同連線來傳送。 

![IoT Edge 中樞是實體裝置與 IoT 中樞之間的閘道](./media/iot-edge-runtime/Gateway.png)

IoT Edge 中樞可以判斷是否已連線到 IoT 中樞。 如果連線中斷，IoT Edge 中樞就會在本機儲存訊息或對應項更新。 一旦連線重新建立之後，就會將所有資料同步。 針對此暫時的快取所使用的位置取決於 IoT Edge 中樞模組對應項的屬性。 快取的大小不會受限，而且只要裝置還有儲存體容量就會持續成長。 For more information, see [Offline capabilities](offline-capabilities.md).

### <a name="module-communication"></a>模組通訊

IoT Edge 中樞可促進模組對模組的通訊。 使用 IoT Edge 中樞作為訊息代理程式，讓模組彼此保持獨立。 模組只需指定它們要在其中接受訊息的輸入，以及要將訊息寫入其中的輸出。 A solution developer can stitch these inputs and outputs together so that the modules process data in the order specific to that solution. 

![IoT Edge 中樞可促進模組對模組的通訊](./media/iot-edge-runtime/module-endpoints.png)

為了將資料傳送至 IoT Edge 中樞，模組會呼叫 SendEventAsync 方法。 第一個引數會指定在哪個輸出上傳送訊息。 The following pseudocode sends a message on **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

若要接收訊息，請註冊會處理來自特定輸入之訊息的回呼。 The following pseudocode registers the function messageProcessor to be used for processing all messages received on **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

For more information about the ModuleClient class and its communication methods, see the API reference for your preferred SDK language: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

解決方案開發人員會負責指定規則，以判斷 IoT Edge 中樞在模組之間傳遞訊息的方式。 Routing rules are defined in the cloud and pushed down to IoT Edge hub in its module twin. 適用於 IoT 中樞路由的相同語法，可用來定義 Azure IoT Edge 中模組之間的路由。 For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

![模組之間的路由會經由 IoT Edge 中樞](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge 代理程式

IoT Edge 代理程式是另一個組成 Azure IoT Edge 執行階段的模組。 它負責將模組具現化，確保它們會繼續執行，並將模組的狀態回報至 IoT 中樞。 This configuration data is written as a property of the IoT Edge agent module twin. 

[IoT Edge 安全性精靈](iot-edge-security-manager.md)會在裝置啟動時啟動 IoT Edge 代理程式。 代理程式會從 IoT 中樞擷取其模組對應項，並檢查部署資訊清單。 部署資訊清單是 JSON 檔案，會宣告需要啟動的模組。 

部署資訊清單中的每個項目都包含關於模組的特定資訊，而 IoT Edge 代理程式可用其來控制模組的生命週期。 以下提供一些更令人感興趣的屬性： 

* **settings.image**：IoT Edge 代理程式用來啟動模組的容器映像。 如果映像受到密碼保護，IoT Edge 代理程式就必須使用適用於容器登錄的認證來設定。 若要從遠端設定容器登錄的認證，您可以藉由使用部署資訊清單來進行，也可以在 IoT Edge 裝置本身上藉由在 IoT Edge 方案資料夾中更新 `config.yaml` 檔案來進行。
* **settings.createOptions** – A string that is passed directly to the Moby container daemon when starting a module’s container. Adding options in this property allows for advanced configurations like port forwarding or mounting volumes into a module’s container.  
* **status**：IoT Edge 代理程式放置模組的狀態。 Usually, this value is set to *running* as most people want the IoT Edge agent to immediately start all modules on the device. 不過，您可以指定要停止之模組的初始狀態，並等候一段時間，以告知 IoT Edge 代理程式啟動模組。 The IoT Edge agent reports the status of each module back to the cloud in the reported properties. 所需屬性和回報的屬性之間的差異可能表示裝置異常。 支援的狀態如下：
   * 正在下載
   * 執行中
   * 狀況不良
   * 失敗
   * 已停止
* **restartPolicy**：IoT Edge 代理程式重新啟動模組的方式。 可能值包括：
   * `never` – The IoT Edge agent never restarts the module.
   * `on-failure` - If the module crashes, the IoT Edge agent restarts it. 如果模組完全關閉，IoT Edge 代理程式就不會重新啟動它。
   * `on-unhealthy` - If the module crashes or is considered unhealthy, the IoT Edge agent restarts it.
   * `always` - If the module crashes, is considered unhealthy, or shuts down in any way, the IoT Edge agent restarts it. 
* **imagePullPolicy** - Whether the IoT Edge agent attempts to pull the latest image for a module automatically or not. If you don't specify a value, the default is *onCreate*. 可能值包括： 
   * `on-create` - When starting a module or updating a module based on a new deployment manifest, the IoT Edge agent will attempt to pull the module image from the container registry.
   * `never` - The IoT Edge agent will never attempt to pull the module image from the container registry. The expectation is that the module image is cached on the device, and any module image updates are made manually or managed by a third-party solution. 

IoT Edge 代理程式會將執行階段回應傳送到 IoT 中樞。 以下是可能回應的清單：
  * 200 - 確定
  * 400 - 部署設定不正確或無效。
  * 417 - The device doesn't have a deployment configuration set.
  * 412 - 部署設定中的結構描述版本無效。
  * 406 - IoT Edge 裝置已離線或無法傳送狀態報表。
  * 500 - IoT Edge 執行階段發生錯誤。

For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

### <a name="security"></a>安全性

IoT Edge 代理程式在 IoT Edge 裝置的安全性中扮演了關鍵角色。 例如，它會執行像是啟動前先驗證模組映像的動作。 

For more information about the Azure IoT Edge security framework, read about the [IoT Edge security manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>後續步驟

[了解 Azure IoT Edge 模組](iot-edge-modules.md)
