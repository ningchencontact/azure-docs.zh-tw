---
title: 了解 Azure 資訊安全中心的 IoT 安全性模組對應項的結果預覽 |Microsoft Docs
description: 深入了解安全性模組對應項以及如何使用 Azure 資訊安全中心的 IoT 的概念。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: c5814b188c73ea03094d7dae565e40ca09e705c2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862432"
---
# <a name="security-module"></a>安全性模組

> [!IMPORTANT]
> 適用於 IoT 的 Azure 資訊安全中心目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

這篇文章說明 Azure 資訊安全中心 (ASC) 設定為 IoT 如何使用裝置對應項和模組。 

## <a name="device-twins"></a>裝置對應項

對 Azure 內所建置的 IoT 解決方案來說，裝置對應項在裝置管理和程序自動化中同時扮演重要的角色。  

適用於 IoT 的 ASC 可與您現有的 IoT 裝置管理平台完全整合，讓您能夠管理裝置的安全性狀態，以及利用現有的裝置控制功能。 整合之後，即可利用 IoT 中樞的對應項的機制。  

深入了解的概念[攣生裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)Azure IoT 中樞。 

## <a name="security-module-twins"></a>安全性模組對應項

Iot 的 ASC 會維護每個裝置在服務中的安全性模組對應項。
安全性模組對應項存放的資訊與相關裝置的安全性，針對每個特定的裝置在您的解決方案。
裝置的安全性內容會維護專屬的安全性模組對應項進行更安全的通訊，以及啟用更新及維護需要較少的資源中。  

請參閱[建立安全性模組對應項](quickstart-create-security-twin.md)並[設定安全性代理程式](how-to-agent-configuration.md)以了解如何建立、 自訂和設定的對應項。 請參閱[了解模組對應項](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)若要深入了解在 IoT 中樞模組對應項的概念。 
 

## <a name="see-also"></a>請參閱
- [ASC IoT preview](overview.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
- [安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)