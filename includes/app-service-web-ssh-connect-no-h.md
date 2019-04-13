---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549994"
---
若要使開啟直接的 SSH 工作階段，與您的容器，就應該執行您的應用程式。

將下列 URL 貼到您的瀏覽器並取代\<應用程式名稱 > 的應用程式名稱：

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

如果您還沒有驗證，您被必須向 Azure 訂用帳戶連線。 驗證之後，您會看到瀏覽器中的殼層，您可以在其中執行您容器內的命令。

![SSH 連線](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
