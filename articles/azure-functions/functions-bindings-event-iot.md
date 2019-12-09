---
title: Azure Functions 的 Azure IoT 中樞系結
description: 瞭解如何在 Azure Functions 中使用 IoT 中樞系結。
author: craigshoemaker
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 2940f9e2adff82c100ed347431e8c1d27c30202e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924432"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure Functions 的 Azure IoT 中樞系結

本文說明如何使用 IoT 中樞的 Azure Functions 系結。 IoT 中樞支援是以[Azure 事件中樞](functions-bindings-event-hubs.md)系結為基礎。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>套件 - Functions 1.x

針對 Azure Functions 1.x 版，會在2.x 版中提供 IoT 中樞系結，分別是2.x 版[的 NuGet 套件。](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub 存放庫中。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>封裝-函數2.x 和更新版本

若是函式2.x 和更新版本，請使用[EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)套件3.x 版. x。 套件的原始程式碼位於 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub 存放庫中。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> 雖然下列程式碼範例會使用事件中樞 API，但指定的語法適用于 IoT 中樞函式。

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
