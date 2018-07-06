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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063743"
---
當您在本機開發函式時，您可以從終端機或命令提示字元中使用 Azure Functions Core Tools，來安裝需要的擴充。 

在更新 function.json 檔案以包含函式所需的所有繫結後，請在專案資料夾中執行 `func extensions install` 命令。 此命令會讀取 function.json 檔案，來查看您需要哪些套件，然後加以安裝。

如果您想要安裝特定版本的套件，或是要在編輯 function.json 檔案之前安裝套件，請使用 `func extensions install` 命令搭配套件的名稱，如下列範例所示：

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

以特定版本的套件取代 `<target_version>`，例如 `3.0.0-beta5`。 有效的版本會列在 [NuGet.org](https://nuget.org) 的個別套件頁面上。
