---
title: 使用 Azure Digital Twins 路由事件和訊息 | Microsoft Docs
description: 使用 Azure Digital Twins 將事件和訊息路由至服務端點的概觀
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323775"
---
# <a name="routing-events-and-messages"></a>活動及訊息路線規劃

IoT 解決方案通常會結合數個功能強大的服務，包括儲存體、分析等等。 本文將說明如何將 Azure Digital Twins 應用程式連線到 Azure 分析、AI 和儲存體服務，以更深入的見解和功能來豐富這些應用程式。

## <a name="route-types"></a>路由類型

Digital Twins 提供兩種方法來將 IoT 事件整合至其他 Azure 服務或商務應用程式：

* **路由 Digital Twins 事件**：當空間圖表中的物件發生變更時、收到遙測資料時，或使用者定義函式根據預先定義的條件建立通知時，Azure Digital Twins 事件就會觸發。 使用者可以將這些事件傳送至[事件中樞](https://azure.microsoft.com/services/event-hubs/)、[服務匯流排主題](https://azure.microsoft.com/services/service-bus/)或[事件方格](https://azure.microsoft.com/services/event-grid/)，以執行進一步的處理。

* **路由裝置遙測**：除了路由事件之外，Azure Digital Twins 也可以將未經處理的裝置遙測訊息路由至事件中樞，以取得深入的見解和分析。 Digital Twins 並未處理這些類型的訊息，只是將其轉送至**事件中樞**。

使用者可以指定一個或多個輸出端點來傳送事件或轉送訊息。 事件和訊息會根據這些預先定義的路由喜好設定來傳送至端點。 換句話說，使用者可以指定某一個端點來接收圖表作業事件，並指定另一個端點來接收裝置遙測事件，依此類推。

![Digital Twins 事件路由][1]

通往**事件中樞**的路由會維持遙測訊息的傳送順序，因此，這些訊息抵達端點的順序會與原本接收訊息的順序相同。 **事件方格**和**服務匯流排**則不保證端點收到事件的順序會與事件發生順序相同。 不過，事件結構描述並不包含可用來識別事件抵達端點後之順序的時間戳記。

## <a name="route-implementation"></a>路由實作

Azure Digital Twins 服務目前支援下列 **EndpointTypes**：

* **EventHub**：事件中樞連接字串端點。
* **ServiceBus**：服務匯流排連接字串端點。
* **EventGrid**：事件方格連接字串端點。

Azure Digital Twins 目前支援將下列 **EventTypes** 傳送至所選的端點：

* **DeviceMessages**：從使用者裝置送出並由系統轉送的遙測訊息。
* **TopologyOperation**：變更圖表或圖表中繼資料的作業。 例如，新增或刪除實體 (例如空間)。
* **SpaceChange**：裝置遙測訊息造成的空間計算值變更。
* **SensorChange**：裝置遙測訊息造成的感應器計算值變更。
* **UdfCustom**：來自使用者定義函式的自訂通知。

> [!IMPORTANT]
> 並非所有 **EndpointTypes** 都支援所有 **EventTypes**。
> 請參閱下表，了解每個 **EndpointType** 可接受的 **EventTypes**。

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     X          |         X         |     X       |      X       |   X       |
| **ServiceBus**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>如需如何建立端點的詳細資料，以及事件結構描述的範例，請參閱[端點和輸出](how-to-egress-endpoints.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解公開預覽版的限制，請參閱 [Azure Digital Twins 預覽限制](concepts-service-limits.md)。

若要試用 Azure Digital Twins 的範例，請參閱[尋找可用會議室的快速入門](quickstart-view-occupancy-dotnet.md)。

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png