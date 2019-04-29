---
title: Azure Functions 的 Azure 事件中樞繫結
description: 了解如何在 Azure Functions 中使用 Azure 事件中樞繫結。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, 函數, 事件處理, 動態運算, 無伺服器架構
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 11/08/2017
ms.date: 03/20/2019
ms.author: v-junlch
ms.openlocfilehash: 7ca3ab8e3becf884fdba460f1981430e0c66ef99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104439"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Functions 的 Azure 事件中樞繫結

本文說明如何針對 Azure Functions 使用 [Azure 事件中樞](../event-hubs/event-hubs-what-is-event-hubs.md)繫結。 Azure Functions 支援事件中樞的觸發程序和輸出繫結。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

對於 Azure Functions 1.x 版，[Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 套件 2.x 版中會提供事件中樞繫結。
套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub 存放庫中。


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>套件 - Functions 2.x

對於 Functions 2.x，請使用 [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) 套件 3.x 版。
套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)

<!-- Update_Description: wording update -->