---
title: 比較 Azure IoT 中樞與 Azure 事件中樞 | Microsoft Docs
description: IoT 中樞和事件中樞 Azure 服務的比較，重點在於功能差異和使用案例。 比較的項目包括支援的通訊協定、裝置管理、監視及檔案上傳。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669733"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>將 IoT 裝置連線到 Azure：IoT 中樞和事件中樞

Azure 提供專為不同連線和通訊類型所開發的服務，協助您連接資料與雲端的強大功能。 「Azure IoT 中樞」和「Azure 事件中樞」都是雲端服務，可擷取大量資料並處理或儲存該資料來獲得業務見解。 這兩種服務的共通點在於支援低延遲時間和高可靠性的資料擷取，不過它們是為不同目的而設計的服務。 IoT 中心是为解决将 IoT 设备连接到 Azure 云时的独特要求而开发的，而事件中心是为大数据流式处理而设计的。 Microsoft 建议使用 Azure IoT 中心将 IoT 设备连接到 Azure

Azure IoT 中心是连接 IoT 设备的云网关，它会收集数据并促成业务见解和自动化。 此外，IoT 中樞的功能還能讓裝置與後端系統之間的關係更豐富。 双向通信功能表示既可以从设备接收数据，也可以将命令和策略发回到设备。 例如，使用云到设备的消息传送可以更新属性或调用设备管理操作。 借助云到设备的通信，还可以使用 Azure IoT Edge 将云中情报发送到边缘设备。 IoT 中樞提供的獨特裝置層級身分識別，有助於進一步保護 IoT 解決方案，預防潛在攻擊。 

[Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)是 Azure 的巨量資料串流服務。 它是專為高輸送量資料串流情節所設計的服務，允許客戶每天傳送數十億筆要求。 事件中樞使用分割取用者模型來相應放大您的串流，並且已融入 Azure 的巨量資料和分析服務當中，如 Databricks、串流分析、ADLS 及 HDInsight。 該服務提供像事件中樞擷取和自動擴充之類的功能，能支援您的巨量資料應用程式和解決方案。 此外，IoT 中心还将事件中心用于其遥测数据流路径，因此，IoT 解决方案也会受益于事件中心的强大功能。

概括而言，这两个解决方案都适合大规模的数据引入。 仅凭 IoT 中心就能提供丰富的 IoT 特定功能，使客户在将 IoT 设备连接到 Azure 云后可以最大程度地提高企业价值。  如果您剛踏上 IoT 之旅，入手 IoT 中樞來支援資料擷取情節，將能確保當您需要 IoT 功能來滿足商業和技術需求時，可立即取用完備的 IoT 功能。

下表提供有關針對 IoT 功能評估這兩項服務時，「IoT 中樞」的兩個層級如何與「事件中樞」做比較的詳細資料。 如需有關標準和基本 IoT 中樞層的詳細資訊，請參閱[如何選擇適合的 IoT 中樞層](iot-hub-scaling.md)。

| IoT 功能 | IoT 中樞標準層 | IoT 中樞基本層 | 事件中樞 |
| --- | --- | --- | --- |
| 裝置到雲端傳訊 | ![勾選][checkmark] | ![勾選][checkmark] | ![勾選][checkmark] |
| 通訊協定：HTTPS、AMQP、透過 WebSocket 的 AMQP | ![勾選][checkmark] | ![勾選][checkmark] | ![勾選][checkmark] |
| 通訊協定：MQTT、透過 WebSocket 的 MQTT | ![勾選][checkmark] | ![勾選][checkmark] |  |
| 每部裝置身分識別 | ![勾選][checkmark] | ![勾選][checkmark] |  |
| 從裝置上傳檔案 | ![勾選][checkmark] | ![勾選][checkmark] |  |
| 裝置佈建服務 | ![勾選][checkmark] | ![勾選][checkmark] |  |
| 雲端到裝置傳訊 | ![勾選][checkmark] |  |  |
| 裝置對應項和裝置管理 | ![勾選][checkmark] |  |  |
| 裝置串流 (預覽) | ![勾選][checkmark] |  |  |
| IoT Edge | ![勾選][checkmark] |  |  |

即使唯一的使用案例是裝置到雲端資料擷取，仍強烈建議您使用「IoT 中樞」，因為它可提供專為 IoT 裝置連線設計的服務。 

### <a name="next-steps"></a>後續步驟

若要進一步探索「IoT 中樞」的功能，請參閱 [IoT 中樞開發人員指南](iot-hub-devguide.md)。

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
