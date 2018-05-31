---
title: 包含檔案
description: 包含檔案
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367370"
---
### <a name="run-the-service"></a>執行服務

1. 按 F5 (或在終端機視窗中輸入 `azds up`) 來執行服務。 服務會自動在您新選取的空間 `scott` 中執行。 
1. 您可以藉由再次執行 `azds resource list`，確認您的服務是在其本身的空間中執行。 首先，您會注意到 `mywebapi` 的執行個體現在是在 `scott` 空間中執行 (在 `default` 中執行的版本仍在執行中，但是不會列出)。 其次，`webfrontend` 的存取點 URL 前面會加上文字 *scott.s.*。 此 URL 對於 `scott` 空間是唯一的，並且表示傳送到 "scott URL" 的要求會嘗試先路由傳送到 `scott` 空間中的服務，然後切換回 `default` 空間中的服務。

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Azure Dev Spaces 的這個內建功能，可讓您在共用環境中端對端測試程式碼，而不需要每位開發人員在其空間中重新建立服務的完整堆疊。 這個路由需要您的應用程式程式碼才能轉送傳播標頭，如本指南的上一個步驟所述。

## <a name="test-code-in-a-space"></a>在空間中測試程式碼
若要搭配 `webfrontend` 來測試您的新版本 `mywebapi`，請在瀏覽器中開啟到 webfrontend 的公用存取點 URL，並且移至 [關於] 頁面。 您應該會看到新訊息顯示。

現在，移除 URL 的 "scott.s." 部分，然後重新整理瀏覽器。 您應該會看到舊的行為 (由在 `default` 中執行的 `mywebapi` 版本展現)