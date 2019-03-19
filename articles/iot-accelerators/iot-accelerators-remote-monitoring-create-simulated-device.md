---
title: 裝置模擬與 IoT 遠端監視 - Azure | Microsoft Docs
description: 本操作指南示範如何搭配遠端監視解決方案加速器使用裝置模擬器。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 81efd9dc5d33ed23574b1cb66f26ccb444d5a3ff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180529"
---
# <a name="create-and-test-a-new-simulated-device"></a>建立及測試新模擬裝置

遠端監視解決方案加速器可讓您定義自己的模擬裝置。 本文會示範如何定義新的模擬燈泡裝置，然後在本機進行測試。 解決方案加速器中包含冷卻器和卡車等模擬裝置。 不過，您可以定義自己的模擬裝置，以在部署實際裝置之前，先測試您的 IoT 解決方案。

> [!NOTE]
> 本文說明如何使用裝置模擬服務中裝載的模擬裝置。 如果您想要建立真實裝置，請參閱[將裝置連線到遠端監視解決方案加速器](iot-accelerators-connecting-devices.md)。

本操作指南會示範如何自訂裝置模擬微服務。 此微服務會是遠端監視解決方案加速器的一部份。 為說明裝置模擬的功能，本操作指南會使用 Contoso IoT 應用程式中的兩個情節：

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>後續步驟

本指南已示範如何建立自訂的模擬裝置類型，並藉由在本機執行裝置模擬微服務來進行測試。

接下來，建議您了解如何將自訂模擬裝置類型部署至[遠端監視解決方案加速器](iot-accelerators-remote-monitoring-deploy-simulated-device.md)。
