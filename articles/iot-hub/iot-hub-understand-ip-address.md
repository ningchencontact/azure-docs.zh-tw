---
title: 瞭解 IoT 中樞的 IP 位址 |Microsoft Docs
description: 瞭解如何查詢您的 IoT 中樞 IP 位址及其屬性。 IoT 中樞的 IP 位址可能會在特定情況下變更，例如嚴重損壞修復或區域性容錯移轉。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383906"
---
# <a name="iot-hub-ip-addresses"></a>IoT 中樞 IP 位址

IoT 中樞的 IP 位址首碼會定期發佈在*AzureIoTHub* [服務](../virtual-network/service-tags-overview.md)標籤底下。 為確保作業正常運作，您的 IoT 裝置必須具有輸出連線能力，才能連到*AzureIoTHub* service 標記底下所列的位址首碼。 您的 IoT 應用程式服務必須額外擁有輸出連線能力，才能連至 [ *EventHub*服務標記] 底下所列的位址首碼。


## <a name="best-practices"></a>最佳作法

* IoT 中樞的 IP 位址首碼可能會變更。 這些變更會在生效之前透過服務標籤定期發佈。 因此，請務必開發處理常式，以定期取得並使用最新的服務標記。 此程式可透過[服務標記探索 API](../virtual-network/service-tags-overview.md#service-tags-in-on-premises)來自動化。
* 使用*AzureIoTHub。 [區功能變數名稱稱]* 標記，用以識別特定區域中的 IoT 中樞端點所使用的 IP 首碼。 若要考慮資料中心的嚴重損壞修復，或[區域性容錯移轉](iot-hub-ha-dr.md)，請同時啟用您 IoT 中樞地理配對區域的 IP 首碼連線能力。


## <a name="support-for-ipv6"></a>IPv6 的支援 

IoT 中樞目前不支援 IPv6。