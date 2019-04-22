---
title: 在 Linux 上建立 Docker/Go 應用程式 - Azure App Service
description: 如何為 Web App for Containers 部署執行 Go 應用程式的 Docker 映像。
keywords: azure app service, web 應用程式, go, docker, 容器
services: app-service
author: msangapu
manager: jeconnoc
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 88c9996ce3f2d89ae58881c913f6bd4e549b5814
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547299"
---
# <a name="run-a-custom-linux-container-in-azure-app-service"></a>在 Azure App Service 中執行自訂的 Linux 容器

[App Service Linux](app-service-linux-intro.md) 可在 Linux 上提供預先定義的應用程式堆疊，且支援 .NET、PHP、Node.js 等其他語言。 您也可以使用自訂 Docker 映像，在尚未於 Azure 中定義的應用程式堆疊上執行 Web 應用程式。 本快速入門示範如何建立 Web 應用程式，以及從 Docker Hub 部署 Go 映像。 [使用 Azure CLI 建立 Web 應用程式](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)

![在 Azure 中執行的範例應用程式](media/quickstart-docker-go/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>建立 Web 應用程式

使用 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 命令，在 `myAppServicePlan` App Service 方案中建立 [Web 應用程式](../overview.md)。 別忘了以全域唯一的應用程式名稱取代 `<app name>`。

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/azure-appservices-go-quickstart
```

在上述命令中，`--deployment-container-image-name` 會指向公用 Docker Hub 映像 [microsoft/azure-appservices-go-quickstart](https://hub.docker.com/r/microsoft/azure-appservices-go-quickstart/)。

建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>瀏覽至應用程式

```bash
http://<app_name>.azurewebsites.net/hello
```

![在 Azure 中執行的範例應用程式](media/quickstart-docker-go/hello-world-in-browser.png)

**恭喜！** 您已將執行 Go 應用程式的自訂 Docker Hub 映像部署至 Web App for Containers。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：從私人容器存放庫部署](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [設定自訂容器](configure-custom-container.md)

> [!div class="nextstepaction"]
> [教學課程：多容器 WordPress 應用程式](tutorial-multi-container-app.md)
