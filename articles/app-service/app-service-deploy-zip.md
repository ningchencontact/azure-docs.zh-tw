---
title: "使用 ZIP 檔案，將您的應用程式部署至 Azure App Service |Microsoft 文件"
description: "了解如何使用 ZIP 檔案，將您的應用程式部署至 Azure 應用程式服務。"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: a0e4df0ef0a1c873f1efcac1d8dbfe3cada18218
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>使用 ZIP 檔案，將您的應用程式部署至 Azure App Service

本文章將示範如何使用來部署您的 web 應用程式的 ZIP 檔案[Azure App Service](app-service-web-overview.md)。 

這個 ZIP 檔案部署會使用相同的 Kudu 服務該 powers 連續整合為基礎的部署。 Kudu 為 ZIP 檔案部署支援下列功能： 

- 刪除的檔案中剩餘的先前部署。
- 若要開啟預設建置流程中，其中包含封裝還原選項。
- [部署自訂](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)，包括執行部署指令碼。  
- 部署記錄檔。 

如需詳細資訊，請參閱[Kudu 文件](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

## <a name="prerequisites"></a>必要條件

若要完成這篇文章中的步驟：

* [建立 App Service 應用程式](/azure/app-service/)，或使用您針對另一個教學課程建立的應用程式。

## <a name="create-a-project-zip-file"></a>建立專案的 ZIP 檔案

>[!NOTE]
> 如果您已經下載 ZIP 檔案中的檔案，請先解壓縮檔案。 例如，如果您從 GitHub 下載 ZIP 檔案，您無法部署該檔案儲存為-是。 GitHub 加入其他巢狀的目錄，而不使用應用程式服務。 
>

在本機的終端機視窗，瀏覽至您的應用程式專案的根目錄。 

這個目錄應該包含您的 web 應用程式的項目檔案，例如_index.html_，_為 index.php_，和_app.js_。 它也可以包含封裝管理檔案，像是_project.json_， _composer.json_， _package.json_， _bower.json_，和_requirements.txt_。

在專案中建立所有項目 ZIP 的封存。 下列命令會使用預設的工具，在您的終端機：

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>ZIP 檔案上傳至雲端殼層

如果您選擇從您的本機終端機請改為執行 Azure CLI，請略過此步驟。

將 ZIP 檔案上傳到雲端殼層遵循此處的步驟。 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

如需詳細資訊，請參閱[保存在 Azure 雲端介面中的檔案](../cloud-shell/persisting-shell-storage.md)。

## <a name="deploy-zip-file"></a>部署 ZIP 檔案

部署上傳的 ZIP 檔案至 web 應用程式使用[az webapp 部署來源組態-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip)命令。 如果您選擇不使用雲端殼層，請確定您的 Azure CLI 版本是 2.0.21 或更新版本。 若要查看哪些版本，請執行`az --version`命令在本機的終端機視窗。 

下列範例會將部署您上傳的 ZIP 檔案。 當使用 Azure CLI 的本機安裝，指定您的本機的 ZIP 檔案的路徑`--src`。   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

此命令會將部署檔案和目錄的 ZIP 檔案到預設應用程式服務應用程式資料夾 (`\home\site\wwwroot`) 並重新啟動應用程式。 如果已設定任何其他自訂的建置程序，它也會執行。 如需詳細資訊，請參閱[Kudu 文件](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

若要檢視此應用程式的部署清單中，您必須使用 REST Api 時 （請參閱下一節）。 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>後續步驟

如需更多的進階部署案例，請嘗試[使用 Git 部署至 Azure ](app-service-deploy-local-git.md)。 Git 型部署至 Azure 可啟用版本控制、封裝還原、MSBuild 等等。

## <a name="more-resources"></a>其他資源

* [從 zip 檔案部署 kudu:](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service 部署認證](app-service-deploy-ftp.md)
