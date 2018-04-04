---
title: 使用用於容器的 Web App 從 Docker 容器登錄進行持續部署 - Azure | Microsoft Docs
description: 如何在用於容器的 Web App 中設定從 Docker 容器登錄進行持續部署。
keywords: azure app service, linux, docker, acr,oss
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>使用用於容器的 Web 應用程式進行持續部署

在本教學課程中，您可以從受控 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 存放庫或 [Docker Hub](https://hub.docker.com) 設定自訂容器映像的持續部署。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="enable-the-continuous-deployment-feature"></a>啟用持續部署功能

您可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 並執行下列命令來啟用持續部署功能：

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

在 [Azure 入口網站](https://portal.azure.com/)中，選取頁面左側的 [App Service] 選項。

選取您要設定 Docker Hub 持續部署的應用程式名稱。

在 [Docker 容器] 頁面上，選取 [開啟]，然後選取 [儲存] 以啟用持續部署。

![應用程式設定的螢幕擷取畫面](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>準備 Webhook URL

使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 並執行下列命令來取得 Webhook URL：

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

針對 Webhook URL，您需要下列端點︰`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`。

您可以取得 `publishingusername` 和 `publishingpwd`，方法是使用 Azure 入口網站來下載 Web 應用程式發佈設定檔。

![新增 Webhook 的螢幕擷取畫面 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>新增 Webhook

### <a name="azure-container-registry"></a>Azure Container Registry

1. 在您的登錄入口網站頁面上，選取 [Webhook]。
2. 若要建立新的 Webhook，請選取 [新增]。 
3. 在 [建立 Webhook] 窗格中，提供您的 Webhook 名稱。 針對 Webhook URI，提供在上一節中取得的 URL。

請確定您將範圍定義為包含您容器映像的存放庫。

![Webhook 的螢幕擷取畫面](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

當您更新映像時，Web 應用程式會以新的映像自動更新。

### <a name="docker-hub"></a>Docker Hub

在您的 [Docker Hub] 頁面上，選取 [Webhook]，然後 [建立 WEBHOOK]。

![新增 Webhook 的螢幕擷取畫面 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

針對 Webhook URL，提供您稍早取得的 URL。

![新增 Webhook 的螢幕擷取畫面 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

當您更新映像時，Web 應用程式會以新的映像自動更新。

## <a name="next-steps"></a>後續步驟

* [Linux 上的 Azure App Service 簡介](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [在 Linux 上的 App Service 中建立.NET Core Web 應用程式](quickstart-dotnetcore.md)
* [在 Linux 上的 App Service 中建立 Ruby Web 應用程式](quickstart-ruby.md)
* [在用於容器的 Web App 中部署 Docker/Go Web 應用程式](quickstart-docker-go.md)
* [Linux 上的 Azure App Service 常見問題集](./app-service-linux-faq.md)
* [使用 Azure CLI 管理用於容器的 Web App](./app-service-linux-cli.md)
