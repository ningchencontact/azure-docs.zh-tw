---
title: 本機 Git 部署至 Azure App Service
description: 了解如何啟用本機 Git 部署至 Azure App Service。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 4cbe26055bdbf906223a327ab8cf94bebe9e7998
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>本機 Git 部署至 Azure App Service

本使用說明指南示範如何將程式碼從本機電腦上的 Git 存放庫部署到 [Azure App Service](app-service-web-overview.md)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先決條件

遵循本使用說明指南中的步驟：

* [安裝 Git](http://www.git-scm.com/downloads)。
* 使用您想要部署的程式碼維護本機 Git 存放庫。

若要範例存放庫以跟著執行，請在您的本機終端機視窗中執行下列命令：

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

## <a name="prepare-your-repository"></a>準備您的存放庫

確定您的存放庫根目錄含有專案中的正確檔案。

| 執行階段 | 根目錄檔案 |
|-|-|
| ASP.NET (僅限 Windows) | _*.sln_、_*.csproj_ 或 _default.aspx_ |
| ASP.NET Core | _*.sln_ 或 _*.csproj_ |
| PHP | _index.php_ |
| Ruby (僅限 Linux) | _Gemfile_ |
| Node.js | 含啟動指令碼的 _server.js_、_app.js_, 或 _package.json_ |
| Python (僅限 Windows) | _\*.py_、_requirements.txt_ 或 _runtime.txt_ |
| HTML | _default.htm_、_default.html_、_default.asp_、_index.htm_、_index.html_ 或 _iisstart.htm_ |
| WebJobs | _App\_Data/jobs/continuous_ (適用於持續的 WebJobs) 或 _App\_Data/jobs/triggered_ (適用於觸發的 WebJobs) 底下的 _\<job_name>/run.\<extension>_。 如需詳細資訊，請參閱 [Kudu WebJobs 文件](https://github.com/projectkudu/kudu/wiki/WebJobs) \(英文\)。 |
| Functions | 請參閱 [Azure Functions 的持續部署](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements)。 |

若要自訂部署，您可以在存放庫根目錄中包含 _.deployment_ 檔案。 如需詳細資訊，請參閱[自訂部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) \(英文\) 和[自訂部署指令碼](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) \(英文\)。

> [!NOTE]
> 請務必對您想要部署的所有變更執行 `git commit`。
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>為應用程式啟用 Git

若要為現有的 App Service 應用程式啟用 Git 部署，請在 Cloud Shell 中執行 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git)。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

若要改為建立已啟用 Git 的應用程式，請在 Cloud Shell 中搭配 `--deployment-local-git` 參數執行 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create)。

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` 命令應可提供類似下列輸出的內容：

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="deploy-your-project"></a>部署專案

回到「本機終端視窗」，將 Azure 遠端新增至本機 Git 存放庫。 使用從[為應用程式啟用 Git](#enable-git-for-you-app) 中取得的 Git 遠端 URL 來取代 _\<url>_。

```bash
git remote add azure <url>
```

推送到 Azure 遠端，使用下列命令來部署您的應用程式。 當系統提示輸入密碼時，務必輸入您在[設定部署使用者](#configure-a-deployment-user)中建立的密碼，而不是您用來登入 Azure 入口網站的密碼。

```bash
git push azure master
```

您可能會在輸出中看到執行階段特定的自動化，例如適用於 ASP.NET 的 MSBuild、適用於 Node.js 的 `npm install`，以及適用於 Python 的 `pip install`。 

部署完成之後，您在 Azure 入口網站中的應用程式現在應該會在 [部署選項] 頁面中有您 `git push` 的記錄。

![](./media/app-service-deploy-local-git/deployment_history.png)

瀏覽至您的應用程式以確認已部署內容。

## <a name="troubleshooting"></a>疑難排解

以下是使用 Git 發佈至 Azure 中的 App Service 應用程式時，會遇到的常見錯誤或問題：

---
**徵狀**：`Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**原因**：如果應用程式尚未啟動並執行，就會發生這個錯誤。

**解決方式**：在 Azure 入口網站中啟動應用程式。 當 Web 應用程式停止時，無法使用 Git 部署。

---
**徵狀**：`Couldn't resolve host 'hostname'`

**原因**：如果建立 'azure' 遠端時所輸入的位址資訊不正確，就會發生這個錯誤。

**解決方式**：使用 `git remote -v` 命令，列出所有遠端以及相關聯的 URL。 驗證 'azure' 遠端的 URL 是否正確。 如有需要，移除此遠端並使用正確的 URL 重新建立。

---
**徵狀**：`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**原因**：如果您在 `git push` 期間未指定分支，或未在 `.gitconfig` 中設定 `push.default` 值，就會發生這個錯誤。

**解決方式**：再次執行 `git push`，指定主分支。 例如︰

```bash
git push azure master
```

---
**徵狀**：`src refspec [branchname] does not match any.`

**原因**：如果您嘗試在 'azure' 遠端上推送至主分支以外的分支，就會發生這個錯誤。

**解決方式**：再次執行 `git push`，指定主分支。 例如︰

```bash
git push azure master
```

---
**徵狀**：`RPC failed; result=22, HTTP code = 5xx.`

**原因**︰如果您嘗試透過 HTTPS 推送大型 Git 存放庫，就會發生這個錯誤。

**解決方式**︰變更本機電腦上的 Git 組態以加大 postBuffer

```bash
git config --global http.postBuffer 524288000
```

---
**徵狀**：`Error - Changes committed to remote repository but your web app not updated.`

**原因**：如果您部署包含會指出需要額外模組之 _package.json_ 檔案的 Node.js 應用程式，就會發生這個錯誤。

**解決方式**︰其他包含 'npm ERR!' 的訊息 在發生這個錯誤之前應該就已記錄，並可提供關於此失敗的更多資訊。 下列是此錯誤的已知原因及其對應的 'npm ERR!' message:

* **格式錯誤的 package.json 檔案**：npm ERR! 無法讀取相依性。
* **原生模組沒有適用於 Windows 的二進位檔發佈**：

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      或
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>其他資源

* [專案 Kudu 文件](https://github.com/projectkudu/kudu/wiki)
* [持續部署至 Azure App Service](app-service-continuous-deployment.md)
