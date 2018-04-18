---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
當您在本機開發函式時，您可以從終端機或命令提示字元中使用 Azure Functions Core Tools，來安裝需要的擴充。 下列 `func extensions install` 命令會安裝 Azure Cosmos DB 繫結擴充：

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

以特定版本的套件取代 `<taget_version>`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。
