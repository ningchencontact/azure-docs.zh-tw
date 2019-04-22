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
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 04f08965d161e35a9ae4423ad5d3cf80cb407b8a
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607767"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>在 Linux 上的 Azure App Service 中建立 Python 應用程式 (預覽)

在本快速入門中，您將一個簡單的 Python 應用程式部署到 [Linux 上的 App Service](app-service-linux-intro.md)，它提供了一個可高度擴充、自我修復的 Web 裝載服務。 您可以透過互動式的瀏覽器型 Azure Cloud Shell 使用 Azure 命令列介面 ([Azure CLI](/cli/azure/install-azure-cli))，以便您可以遵循使用 Mac、Linux 或 Windows 電腦的步驟進行操作。

![在 Azure 中執行的範例應用程式](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

* <a href="https://www.python.org/downloads/" target="_blank">安裝 Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">安裝 Git</a>
* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="download-the-sample-locally"></a>將範例下載到本機

在終端機視窗中執行下列命令，將應用程式範例複製到本機電腦，並瀏覽至包含範例程式碼的目錄。

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

存放庫包含 *application.py*，這會向 App Service 指出存放庫中包含 Flask 應用程式。 如需詳細資訊，請參閱[容器的啟動程序和自訂](how-to-configure-python.md)。

## <a name="run-the-app-locally"></a>在本機執行應用程式

在本機執行應用程式，以便您查看它在部署至 Azure 時的樣貌。 開啟終端機視窗，並使用下列命令安裝必要的相依性，然後啟動內建的開發伺服器。 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

開啟網頁瀏覽器，然後巡覽至位於 `http://localhost:5000/` 的範例應用程式。

您會看到來自範例應用程式的 **Hello World!** 訊息顯示在網頁中。

![在本機執行的範例應用程式](media/quickstart-python/hello-world-in-browser.png)

在終端機視窗中，按 **Ctrl+C** 結束 web 伺服器。

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

在下列範例中，使用唯一的應用程式名稱取代 `<app-name>`。

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

此命令可能會花數分鐘執行。 執行上述命令時，會顯示類似下列範例的資訊：

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>瀏覽至應用程式

使用 web 瀏覽器瀏覽至已部署的應用程式。

```bash
http://<app-name>.azurewebsites.net
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

使用 [`az webapp up`](/cli/azure/ext/webapp/webapp?view=azure-cli-latest.md#ext-webapp-az-webapp-up) 命令重新部署應用程式。 將應用程式的名稱替代為 `<app-name>`，並指定 `<location-name>` 的位置 (使用 [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) 命令中其中一個顯示的值)。

```bash
az webapp up -n <app-name> -l <location-name>
```

部署完成後，切換回在**瀏覽至應用程式**步驟中開啟的瀏覽器視窗，然後重新整理頁面。

![在 Azure 中執行的已更新範例應用程式](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 應用程式

移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>，以管理您所建立的應用程式。

按一下左側功能表中的 [應用程式服務]，然後按一下 Azure 應用程式的名稱。

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
> [教學課程：在自訂容器中執行 Python 應用程式](tutorial-custom-docker-image.md)
