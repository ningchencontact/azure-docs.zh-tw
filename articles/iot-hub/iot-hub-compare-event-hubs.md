---
title: 比較 Azure IoT 中樞與 Azure 事件中樞 | Microsoft Docs
description: IoT 中樞和事件中樞 Azure 服務的比較，重點在於功能差異和使用案例。 比較的項目包括支援的通訊協定、裝置管理、監視及檔案上傳。
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Azure IoT 中樞和 Azure 事件中樞的比較

「Azure IoT 中樞」和「Azure 事件中樞」都是雲端服務，可擷取大量資料並處理或儲存該資料來獲得業務見解。 這兩項服務的相似之處在於，它們都支援以低延遲且高可靠性的方式處理事件和遙測資料。 不過，只有「IoT 中樞」是開發成具備支援大規模物聯網案例所需的特定功能。 

「Azure IoT 中樞」是雲端閘道，可連接裝置並收集資料以獲得業務見解和達到自動化。 它可讓您輕鬆將資料串流至雲端並大規模管理您的裝置。 「IoT 中樞」與其他資料擷取服務之間的一個重要差異在於，「IoT 中樞」包含了可強化您裝置與後端系統之間關聯性的功能。 雙向通訊功能意謂著當您從裝置接收資料時，也可以將訊息傳回給裝置來更新屬性或叫用動作。 裝置層級身分識別可協助保護您的系統。 分散式運算會將雲端服務邏輯移到邊緣裝置上。

[Azure 事件中樞][Azure Event Hubs]是一項事件擷取服務，可以處理和儲存大量的資料和遙測。 「事件中樞」是針對資料中心之間及資料中心內部案例的大規模事件擷取而設計的，但並不提供「IoT 中樞」隨附的豐富 IoT 特定功能。 因此，不建議將「事件中樞」用於您的 IoT 解決方案。 

下表提供有關針對 IoT 功能評估這兩項服務時，「IoT 中樞」的兩個層級如何與「事件中樞」做比較的詳細資料。 如需有關標準和基本 IoT 中樞層的詳細資訊，請參閱[如何選擇適合的 IoT 中樞層][lnk-scaling]。

| IoT 功能 | IoT 中樞標準層 | IoT 中樞基本層 | 事件中樞 |
| --- | --- | --- | --- |
| 裝置到雲端傳訊 | ![勾選][1] | ![勾選][1] | ![勾選][1] |
| 通訊協定：HTTPS、AMQP、透過 WebSocket 的 AMQP | ![勾選][1] | ![勾選][1] | ![勾選][1] |
| 通訊協定：MQTT、透過 WebSocket 的 MQTT | ![勾選][1] | ![勾選][1] |  |
| 每部裝置身分識別 | ![勾選][1] | ![勾選][1] |  |
| 從裝置上傳檔案 | ![勾選][1] | ![勾選][1] |  |
| 裝置佈建服務 | ![勾選][1] | ![勾選][1] |  |
| 雲端到裝置傳訊 | ![勾選][1] |  |  |
| 裝置對應項和裝置管理 | ![勾選][1] |  |  |
| IoT Edge | ![勾選][1] |  |  |

即使唯一的使用案例是裝置到雲端資料擷取，仍強烈建議您使用「IoT 中樞」，因為它可提供專為 IoT 裝置連線設計的服務。 

### <a name="next-steps"></a>後續步驟

若要進一步探索「IoT 中樞」的功能，請參閱 [IoT 中樞開發人員指南][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png