---
title: Azure IoT 中樞 - 開始將 IoT 裝置連線到雲端 | Microsoft Docs
description: 了解如何將 IoT 面板和入門套件連線到 Azure IoT 中樞。 您的裝置可以將遙測資料傳送到 IoT 中樞，而 IoT 中樞會監視並管理您的裝置。
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
keywords: Azure IoT 中樞教學課程
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: dafb8aca34a5a41f45f76d526aa3b8f3b1b792c4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Azure IoT 中樞開始使用實際裝置

您可以使用 Azure IoT 中樞和 Azure IoT 裝置 SDK 來建置物聯網 (IoT) 解決方案：

* Azure IoT 中樞是雲端中完全受控的服務，可安全地連線、監視及管理您的 IoT 裝置。 您可以使用 Azure IoT 裝置 SDK 來實作您的 IoT 裝置。
* 在更複雜的 IoT 案例中使用 IoT 閘道。 例如，您在此案例中必須考慮諸如傳統裝置、頻寬成本、安全性和隱私權原則或邊緣資料處理等因素。 在這些情況下，請使用 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) 來實作可將裝置連線到 IoT 中樞的閘道。

## <a name="what-the-how-to-articles-cover"></a>操作說明文章涵蓋內容

這些文章將為您介紹 Azure IoT 中樞與裝置的 SDK。 這些文章涵蓋的常見 IoT 案例會說明 IoT 中樞的功能。 文中也將說明如何將 IoT 中樞與其他 Azure 服務和工具結合，建置更強大的 IoT 解決方案。 在文章中，您使用實際的 IoT 裝置。

## <a name="set-up-your-device"></a>設定您的裝置

將 IoT 裝置或閘道連線至 Azure IoT 中樞：

| IoT 裝置                       | 程式設計語言 |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py]、[Node.js][Pi_Nd]、[C][Pi_C]  |
| IoT DevKit                       | [VSCode 中的 Arduino][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd]、[C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing 開發人員       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| 線上裝置模擬器         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
