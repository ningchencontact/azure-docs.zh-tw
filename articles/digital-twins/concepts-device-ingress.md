---
title: Azure Digital Twins 的裝置連線能力和遙測輸入 |Microsoft Docs
description: 使用 Azure Digital Twins 上架裝置的概觀
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 7eddea7e0d57b89318232da6f086bbe2f649ee77
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211922"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>裝置連線能力與遙測輸入

裝置和感應器所傳送的遙測資料能組成任何 IoT 解決方案的骨幹。 因此，在一個位置內容中表現這些不同的資源並加以管理，是 IoT 應用程式開發的主要考量。 Azure Digital Twins 會使用空間智慧圖表來整合裝置與感應器，藉此簡化 IoT 解決方案的開發。

若要開始，IoT 中樞資源應該建立在空間圖表根部，讓根部空間下的所有裝置可傳送訊息。 建立好 IoT 中樞，而且具有感應器的裝置也在 Digital Twins 執行個體中註冊完成後，裝置就可開始透過 [Azure IoT 裝置 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks) 將資料傳送到 Digital Twins 服務。

上架裝置的逐步指南可在[部署和設定 Digital Twins 的教學課程](tutorial-facilities-setup.md)中取得。 快速瀏覽後，步驟如下：

- 從 [Azure 入口網站](https://portal.azure.com)部署 Azure Digital Twins 執行個體
- 在圖表中建立空間
- 建立 IoT 中樞資源，並將其指派給圖表中的空間
- 在圖表中建立裝置和感應器，並將它們指派給前面步驟中建立的空間
- 建立比對器，以根據條件篩選遙測訊息
- 建立[使用者定義函式](concepts-user-defined-functions.md)，並將其指派給圖表中的空間，以透過自訂方式處理遙測訊息
- 指派角色以允許使用者定義函式存取圖表資料
- 從 Digital Twins 管理 API 中取得 IoT 中樞裝置連接字串
- 使用 Azure IoT 裝置 SDK 在裝置上設定裝置連接字串

接下來您將了解如何從 Digital Twins 管理 API 中取得 IoT 中樞裝置連接字串，以及如何使用 IoT 中樞遙測訊息格式來傳送以感應器為基礎的遙測。 Digital Twins 會要求所收到的每個遙測片段，都要與空間圖表內的感應器相關聯，以確保資料會在適當的空間內容中進行處理和路由。

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>從管理 API 中取得 IoT 中樞裝置連接字串

使用 `includes=ConnectionString` 參數在裝置 API 上執行 GET 呼叫，以取得 IoT 中樞裝置連接字串。 您可以篩選裝置 GUID 或硬體識別碼，以找出指定的裝置：

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| 自訂屬性名稱 | 取代為 |
| --- | --- |
| *yourManagementApiUrl* | 管理 API 的完整 URL 路徑 |
| *yourDeviceGuid* | 裝置識別碼 |
| *yourDeviceHardwareId* | 裝置硬體識別碼 |

在回應承載中，複製裝置的 *connectionString* 屬性；當您呼叫 Azure IoT 裝置 SDK 將資料傳送至 Azure Digital Twins 時，會用到此屬性。

## <a name="device-to-cloud-message"></a>裝置到雲端的訊息

您可以自訂裝置的訊息格式和承載，以符合您解決方案的需求。 您可以使用任何可序列化為位元組陣列或資料流，並且由 [Azure IoT 裝置用戶端訊息類別 Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) 所支援的資料合約。 訊息可以是自訂的二進位格式，但您必須在對應的使用者定義函式中解碼資料合約。 裝置到雲端訊息的唯一需求是要維護一組屬性 (如下所示)，以確保您的訊息會正確地路由至處理引擎。

### <a name="telemetry-properties"></a>遙測屬性

雖然 **Message** 的承載內容可以是大小上限為 256 KB 的任意資料，但在預期的 [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) \(英文\) 上會有一些要求。 下面步驟會反映系統支援的必要和選擇性屬性：

| 屬性名稱 | 值 | 必要 | 說明 |
|---|---|---|---|
| *DigitalTwins-Telemetry* | 1.0 | 是 | 識別系統訊息的常數值 |
| *DigitalTwins-SensorHardwareId* | `string(72)` | 是 | 用於傳送 **Message** 之感應器的唯一識別碼。 此值必須符合物件的 **HardwareId** 屬性，才能讓系統進行處理。 例如， `00FF0643BE88-CO2` |
| *CreationTimeUtc* | `string` | 否 | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 格式的日期字串，用來識別承載的取樣時間。 例如， `2018-09-20T07:35:00.8587882-07:00` |
| *CorrelationId* | `string` | 否 | 可用來追蹤系統上事件的 UUID。 例如， `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>將訊息傳送至 Digital Twins

使用 DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) 或 [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) 呼叫，將訊息傳送至 Digital Twins 服務。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Digital Twins 的資料處理和使用者定義函式功能，請閱讀 [Azure Digital Twins 資料處理和使用者定義函式](concepts-user-defined-functions.md)。

