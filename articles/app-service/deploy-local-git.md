---
title: 從本機 Git 存放庫部署 - Azure App Service
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
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836936"
---
# <a name="local-git-deployment-to-azure-app-service"></a>本機 Git 部署至 Azure App Service

本作法指南說明如何部署您的應用程式[Azure App Service](overview.md)從本機電腦上的 Git 儲存機制。

## <a name="prerequisites"></a>先決條件

遵循本使用說明指南中的步驟：

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [安裝 Git](https://www.git-scm.com/downloads)。
  
- 具有您想要部署的程式碼的本機 Git 存放庫。 若要下載的範例存放庫，請在您的本機終端機視窗中執行下列命令：
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>使用 Kudu 組建伺服器部署

若要啟用您的應用程式與 Kudu 應用程式服務組建伺服器的本機 Git 部署的最簡單方式是使用 Azure Cloud Shell。 

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>取得部署的 URL

若要取得的 URL，若要啟用現有的應用程式的本機 Git 部署，請執行[ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git)在 Cloud Shell 中。 取代\<應用程式名稱 > 和\<群組名稱 > 與您的應用程式和其 Azure 資源群組的名稱。

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

或者，若要建立新 Git 功能的應用程式，執行[ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)在 Cloud Shell 中使用`--deployment-local-git`參數。 取代\<應用程式名稱 >，\<群組名稱 >，並\<計劃名稱 > 與新的 Git 應用程式、 其 Azure 資源群組和其 Azure App Service 方案的名稱。

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

兩個命令會傳回之類的 URL: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`。 若要部署您的應用程式，在下一個步驟中使用此 URL。

而不是使用此帳戶層級的 URL，您也可以使用應用程式層級的認證來啟用本機 Git。 Azure App Service 會自動產生這些認證以供每個應用程式。 

在 Cloud Shell 中執行下列命令，以取得應用程式認證。 取代\<應用程式名稱 > 和\<群組名稱 > 使用您的應用程式名稱和 Azure 資源群組名稱。

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

使用您的應用程式，在下一個步驟中，將會傳回的 URL。

### <a name="deploy-the-web-app"></a>部署 Web 應用程式

1. 開啟 本機 Git 儲存機制，本機終端機視窗，並將 Azure 遠端新增。 在下列命令中，取代\<url > 部署使用者特定 URL 或您從上一個步驟取得的應用程式特定 URL。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 推送至 Azure 遠端使用`git push azure master`。 
   
1. 在 [ **Git 認證管理員**] 視窗中，輸入您[部署的使用者密碼](#configure-a-deployment-user)，取代 Azure 登入密碼。
   
1. 檢閱輸出。 您可能會看到執行階段特定的自動化，例如 asp.net、 MSBuild`npm install`適用於 Node.js，和`pip install`適用於 Python。 
   
1. 瀏覽至您的應用程式，以確認內容已部署在 Azure 入口網站。

## <a name="deploy-with-azure-pipelines-builds"></a>部署與 Azure 管線的組建

如果您的帳戶具有必要的權限，您可以設定 Azure 管線 （預覽） 來啟用應用程式的本機 Git 部署。 

- 您的 Azure 帳戶必須具有寫入權限加入 Azure Active Directory 建立服務。 
  
- 您的 Azure 帳戶必須擁有**擁有者**您 Azure 訂用帳戶中的角色。

- 您必須是系統管理員可以在您想要使用 Azure DevOps 專案。

若要啟用您的應用程式與 Azure 管線 （預覽） 的本機 Git 部署：

1. 瀏覽至 Azure App Service 應用程式頁面中[Azure 入口網站](https://portal.azure.com)，然後選取**部署中心**左側功能表中。
   
1. 在 **部署中心**頁面上，選取**本機 Git**，然後選取**繼續**。 
   
   ![選取 本機 Git，然後選取 繼續](media/app-service-deploy-local-git/portal-enable.png)
   
1. 在 **組建提供者**頁面上，選取**Azure 管線 （預覽）** ，然後選取**繼續**。 
   
   ![選取 Azure 管線 （預覽），然後選取 繼續。](media/app-service-deploy-local-git/pipeline-builds.png)

1. 在 **設定**頁面上，設定新的 Azure DevOps 組織，或指定現有的組織，然後選取**繼續**。
   
   > [!NOTE]
   > 如果未列出您現有的 Azure DevOps 組織，您可能需要將它連結到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱 <<c0> [ 定義您的 CD 發行管線](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。
   
1. 根據您的 App Service 方案[定價層](https://azure.microsoft.com/pricing/details/app-service/plans/)，您可能會看到**部署至預備環境**頁面。 選擇是否要[啟用部署位置](deploy-staging-slots.md)，然後選取**繼續**。
   
1. 在 **摘要**頁面上，檢閱設定，然後再選取**完成**。
   
1. 備妥 Azure 管線時，將複製的 Git 存放庫 URL**部署中心**頁面，即可在下一個步驟中使用。 
   
   ![複製的 Git 存放庫 URL](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. 在本機終端機視窗中，將 Azure 遠端新增至本機 Git 存放庫。 在命令中，取代\<url > 您從上一個步驟取得的 Git 儲存機制的 url。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 推送至 Azure 遠端使用`git push azure master`。 
   
1. 在  **Git 認證管理員**頁面上，使用您的 visualstudio.com 使用者名稱登入。 其他驗證方法，請參閱[Azure DevOps 服務驗證概觀](/vsts/git/auth-overview?view=vsts)。
   
1. 部署完成之後，檢視在建置進度`https://<azure_devops_account>.visualstudio.com/<project_name>/_build`，並在部署進度`https://<azure_devops_account>.visualstudio.com/<project_name>/_release`。
   
1. 瀏覽至您的應用程式，以確認內容已部署在 Azure 入口網站。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>針對部署進行疑難排解

當您使用 Git 來發行至 App Service 應用程式在 Azure 中，您可能會看到下列常見的錯誤訊息：

|Message|原因|解決方案
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|應用程式無法啟動並執行。|在 Azure 入口網站中啟動應用程式。 停止 web 應用程式時，無法使用 Git 部署。|
|`Couldn't resolve host 'hostname'`|在 'azure' 遠端的位址資訊不正確。|使用 `git remote -v` 命令，列出所有遠端以及相關聯的 URL。 驗證 'azure' 遠端的 URL 是否正確。 如有需要，移除此遠端並使用正確的 URL 重新建立。|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|您未指定分支`git push`，或尚未設定`push.default`中的值`.gitconfig`。|執行`git push`同樣地，指定主要分支： `git push azure master`。|
|`src refspec [branchname] does not match any.`|您嘗試上推送至除了主要以外的分支 'azure' 遠端。|執行`git push`同樣地，指定主要分支： `git push azure master`。|
|`RPC failed; result=22, HTTP code = 5xx.`|如果您嘗試透過 HTTPS 推送大型 Git 存放庫，就會發生這個錯誤。|變更要在本機電腦上的 git 組態`postBuffer`更大。 例如： `git config --global http.postBuffer 524288000` 。|
|`Error - Changes committed to remote repository but your web app not updated.`|部署 Node.js 應用程式與_package.json_檔案指出需要額外的模組。|檢閱`npm ERR!`這個錯誤，如需詳細的內容，在失敗之前的錯誤訊息。 以下是這個錯誤，而且對應的已知的原因`npm ERR!`訊息：<br /><br />**格式不正確的 package.json 檔案**: `npm ERR! Couldn't read dependencies.`<br /><br />**原生模組不會對 Windows 的二進位檔發佈**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />或 <br />`npm ERR! [modulename@version]，預先安裝： \make || gmake\`|

## <a name="additional-resources"></a>其他資源

- [專案 Kudu 文件](https://github.com/projectkudu/kudu/wiki)
- [持續部署至 Azure App Service](deploy-continuous-deployment.md)
- [範例：建立 web 應用程式和部署程式碼從本機 Git 存放庫 (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [範例：建立 web 應用程式和部署程式碼從本機 Git 存放庫 (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
