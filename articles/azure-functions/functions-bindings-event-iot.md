---
title: 適用於 Azure Functions 的 azure IoT 中樞繫結
description: 了解如何在 Azure Functions 中使用 IoT 中樞繫結。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, 函數, 事件處理, 動態運算, 無伺服器架構
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: e71e102a5a6df44e6bdd6a845540de3cbbef98f3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57457493"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>適用於 Azure Functions 的 azure IoT 中樞繫結

這篇文章說明如何使用 IoT 中樞的 Azure Functions 繫結。 IoT 中樞支援根據[Azure 事件中樞繫結](link to event hub doc)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

如需 Azure Functions 版本 1.x 中提供的 IoT 中樞 」 繫結[Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 套件版本 2.x。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub 存放庫中。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>套件 - Functions 2.x

對於 Functions 2.x，請使用 [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) 套件 3.x 版。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> 下列程式碼範例會使用事件中樞 API，而指定的語法是適用於 IoT 中樞函式。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
