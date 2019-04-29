---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 9f7c82a2bd35f06da096fa0dd9d5d1fa4d08011e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838925"
---
使用 Cloud Shell，在 `myAppServicePlan` App Service 方案中建立 [Web 應用程式](../articles/app-service/overview.md)。 您可以使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 命令執行此工作。 在下列範例中，取代*\<應用程式名稱 >* 全域唯一的應用程式名稱 (有效字元為`a-z`， `0-9`，和`-`)。

```azurecli-interactive
az webapp create --name <app-name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的資訊：

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

您已建立空的 Web 應用程式，其中已啟用 Git 部署。

> [!NOTE]
> Git 遠端的 URL 會顯示在 `deploymentLocalGitUrl` 屬性中，其格式為 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`。 儲存此 URL，稍後您會用到此資訊。
>

瀏覽至新建立的 Web 應用程式。

```
http://<app-name>.azurewebsites.net
```

新的 Web 應用程式看起來應該像這樣：
