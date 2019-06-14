---
title: 包含檔案
description: 包含檔案
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66131425"
---
延伸模組套件組合請透過在設定 Azure Functions 小組所發行的所有繫結*host.json*檔案。 針對本機開發，請確定您有最新版[Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)。

若要使用延伸模組套件組合，更新*host.json*檔案，以包含下列項目`extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- `id`屬性參照 Microsoft Azure 函式延伸模組套件組合的命名空間。
- `version`參考套件組合的版本。

以套件組合變更的套件組合版本遞增。 只有當套件組合中的移動主要版本時，才發生主要版本變更。 `version`屬性使用[間隔標記法來指定版本範圍](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)。 Functions 執行階段一律會挑選版本範圍或間隔所定義的最高允許版本。

一旦您在專案中參考延伸模組套件組合，然後所有的預設繫結可供您的函式。 提供的繫結[延伸模組套件組合](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)是：

|封裝  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|