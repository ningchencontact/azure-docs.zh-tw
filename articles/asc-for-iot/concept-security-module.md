---
title: 了解 IoT 安全性模組對應項預覽 ASC |Microsoft Docs
description: 深入了解安全性模組對應項和如何用於 ASC iot 的概念。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 63d21cc5027145ab87030bd2561bc5087298f16c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541838"
---
# <a name="security-module"></a>安全性模組

> [!IMPORTANT]
> Iot 的 ASC 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

這篇文章說明 iot ASC 如何使用裝置對應項和模組。 

## <a name="device-twins"></a>裝置對應項

在 Azure 中建置 IoT 解決方案，裝置對應項會扮演重要的角色，在 裝置管理與程序自動化。  

Iot 的 ASC 提供完整的整合，與您現有 IoT 裝置管理平台，可讓您管理您裝置的安全性狀態以及進行中，使用現有的裝置控制功能。 整合之後，即可利用 IoT 中樞的對應項的機制。  

深入了解的概念[裝置](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)azure 的 Azure IoT 中樞對應項。 

## <a name="security-module-twins"></a>安全性模組對應項

Iot 的 ASC 會維護每個裝置在服務中的安全性模組對應項。 安全性模組對應項存放的資訊與相關裝置的安全性，針對每個特定的裝置在您的解決方案。 裝置的安全性內容會維護專屬的安全性模組對應項進行更安全的通訊，以及啟用更新及維護需要較少的資源中。  

請參閱[建立安全性模組對應項](quickstart-create-security-twin.md)並[設定安全性代理程式](concept-agent-configuration.md)以了解如何建立、 自訂和設定的對應項。 請參閱[了解模組對應項](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)若要深入了解在 IoT 中樞模組對應項的概念。 
 

## <a name="see-also"></a>請參閱
- [ASC IoT preview](overview.md)
- [部署安全性代理程式](select-deploy-agent.md)
- [安全性代理程式驗證方法](concept-security-agent-authentication-methods.md)