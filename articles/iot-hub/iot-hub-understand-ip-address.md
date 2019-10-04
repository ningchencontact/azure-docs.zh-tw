---
title: 瞭解 IoT 中樞的 IP 位址 |Microsoft Docs
description: 瞭解如何查詢您的 IoT 中樞 IP 位址及其屬性。 IoT 中樞的 IP 位址可能會在特定情況下變更，例如嚴重損壞修復或區域性容錯移轉。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7af40404550fb78af891563d8256f23620781b24
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841540"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>瞭解 IoT 中樞的 IP 位址

IoT 中樞的 IP 位址是您中樞的負載平衡公用端點，而不是專用的 IP 位址。 此位址取決於其部署所在的 Azure 區域的網路位址範圍。 此 IP 位址可能會變更，恕不另行通知。 資料中心網路更新、資料中心嚴重損壞修復或 IoT 中樞的區域性容錯移轉，都可以變更您的 IoT 中樞 IP 位址。 如需 Azure IoT 中樞區域性容錯移轉和可用性的詳細資訊，請參閱[IoT 中樞高可用性和嚴重損壞修復](iot-hub-ha-dr.md)。

IoT 中樞的 IP 位址會在容錯移轉至另一個區域後變更。 您可以遵循[執行 IoT 中樞的手動容錯移轉](tutorial-manual-failover.md)教學課程來測試這項功能。

## <a name="discover-your-iot-hub-ip-address"></a>探索您的 IoT 中樞 IP 位址

您可以使用 CNAME （[*IoT-Hub-name*]. azure-devices.net）上的反向 DNS 查詢來探索您的 IoT 中樞 IP 位址。 您可以使用**nslookup**來驗證 IoT 中樞實例的 IP 位址：

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

此 IP 位址可能會變更，恕不另行通知。 在容錯移轉或嚴重損壞修復案例中，您必須在次要區域中輪詢您的 IoT 中樞 IP 位址。

## <a name="outbound-firewall-rules-for-iot-hub"></a>IoT 中樞的輸出防火牆規則

請嘗試建立防火牆規則，並根據 IoT 中樞主機名稱或網域進行篩選。 如果您只允許對特定位址的輸出流量，請定期輪詢您的 IoT 中樞 IP 位址並更新您的防火牆規則。

## <a name="support-for-ipv6"></a>IPv6 的支援 

IoT 中樞目前不支援 IPv6。