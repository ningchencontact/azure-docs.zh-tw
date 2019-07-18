---
title: 在 Linux 上建立 Python 應用程式 - Azure App Service | Microsoft Docs
description: 短短幾分鐘內在 Linux 上的 Azure App Service 中部署第一個 Python Hello World 應用程式。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ea247907aebc241fb8f1b266ad55bc2fc983607f
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853981"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>在 Linux 上的 Azure App Service 中建立 Python 應用程式 (預覽)

[Linux 上的 App Service](app-service-linux-intro.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本快速入門說明如何使用 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 在 Linux 上的 App Service 中將 Python 應用程式部署於內建的 Python 映像 (預覽) 之上。

您可以使用 Mac、Windows 或 Linux 機器，依照本文中的步驟操作。

![在 Azure 中執行的範例應用程式](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>下載範例

在 Cloud Shell 中，建立快速入門目錄並變更為此目錄。

```bash
mkdir quickstart

cd quickstart
```

下一步，執行下列命令，將範例應用程式存放庫複製到您的快速入門目錄。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

執行上述命令時，會顯示類似下列範例的資訊：

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>建立 Web 應用程式

變更為包含範例程式碼的目錄，並執行 `az webapp up` 命令。

在下列範例中，使用全域唯一的應用程式名稱 (有效的字元為 `a-z`、`0-9` 和 `-`) 取代 \<app_name>  。

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

此命令可能會花數分鐘執行。 執行上述命令時，會顯示類似下列範例的資訊：

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

`az webapp up` 命令會執行下列動作：

- 建立預設的資源群組。

- 建立預設的 App Service 方案。

- 建立具有所指定名稱的應用程式。

- [以 Zip 檔進行部署](https://docs.microsoft.com/azure/app-service/deploy-zip)，將目前工作目錄中的檔案部署到應用程式。

## <a name="browse-to-the-app"></a>瀏覽至應用程式

使用 web 瀏覽器瀏覽至已部署的應用程式。

```bash
http://<app_name>.azurewebsites.net
```

具有內建映像的 Python 程式碼範例正在 Linux 上的 App Service 中執行。

![在 Azure 中執行的範例應用程式](media/quickstart-python/hello-world-in-browser.png)

**恭喜！** 您已將第一個 Python 應用程式部署至 Linux 上的 App Service。

## <a name="update-locally-and-redeploy-the-code"></a>在本機更新和重新部署程式碼

在 Cloud Shell 中，輸入 `code application.py` 以開啟 Cloud Shell 編輯器。

![Code application.py](media/quickstart-python/code-applicationpy.png)

 在呼叫 `return` 時，對文字進行小幅變更：

```python
return "Hello Azure!"
```

儲存您的變更並結束編輯器。 使用 `^S` 命令進行儲存，以及使用 `^Q` 來結束作業。

您現在將重新部署應用程式。 使用應用程式替代 `<app_name>`。

```bash
az webapp up -n <app_name>
```

部署完成後，切換回在**瀏覽至應用程式**步驟中開啟的瀏覽器視窗，然後重新整理頁面。

![在 Azure 中執行的已更新範例應用程式](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 應用程式

移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，以管理您所建立的應用程式。

按一下左側功能表中的 [應用程式服務]  ，然後按一下 Azure 應用程式的名稱。

![入口網站瀏覽至 Azure 應用程式](./media/quickstart-python/app-service-list.png)

您會看到應用程式的 [概觀] 頁面。 您可以在這裡執行基本管理工作，像是瀏覽、停止、啟動、重新啟動及刪除。

![Azure 入口網站中的 App Service 頁面](media/quickstart-python/app-service-detail.png)

左側功能表提供不同的頁面來設定您的應用程式。 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

在 Linux 上的 App Service 中，內建的 Python 映像目前處於預覽狀態，您可以自訂用來啟動應用程式的命令。 您也可以改用自訂容器來建立生產 Python 應用程式。

> [!div class="nextstepaction"]
> [教學課程：使用 PostgreSQL 的 Python 應用程式](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [設定 Python 應用程式](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [教學課程：從私人容器存放庫部署](tutorial-custom-docker-image.md)
