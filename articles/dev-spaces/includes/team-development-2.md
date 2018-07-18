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
ms.openlocfilehash: 85f8632aae8a70b1282155881dbca6b25734a6c5
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936392"
---
### <a name="run-the-service"></a>執行服務

1. 按 F5 (或在終端機視窗中輸入 `azds up`) 來執行服務。 服務會自動在您新選取的空間 `default/scott` 中執行。 
1. 您可以藉由再次執行 `azds list`，確認您的服務是在其本身的空間中執行。 首先，您會注意到 `mywebapi` 的執行個體現在是在 `default/scott` 空間中執行 (在 `default` 中執行的版本仍在執行中，但是不會列出)。 其次，`webfrontend` 的存取點 URL 前面會加上文字 "scott.s."。 此 URL 是 `default/scott` 空間專屬的。 此特殊 URL 表示傳送到  "scott URL" 的要求會嘗試先路由傳送到 `default/scott` 空間中的服務，但若失敗，則會切換回 `default` 空間中的服務。

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Azure Dev Spaces 的這項內建功能可讓您在共用空間中測試程式碼，而不需要每位開發人員在其空間中重新建立服務的完整堆疊。 這個路由需要您的應用程式程式碼才能轉送傳播標頭，如本指南的上一個步驟所述。

### <a name="test-code-in-a-space"></a>在空間中測試程式碼
若要測試新版的 `mywebapi` 與 `webfrontend` 的搭配運作情形，請開啟瀏覽器並進入 `webfrontend` 的公用存取點 URL，然後移至 [關於] 頁面。 您應該會看到新訊息顯示。

現在，移除 URL 的 "scott.s." 部分，然後重新整理瀏覽器。 您應該會看到舊的行為 (使用在 `default` 中執行的 `mywebapi` 版本)
