---
title: "使用 ZIP 檔案將您的應用程式部署至 Azure App Service | Microsoft Docs"
description: "了解如何使用 ZIP 檔案將您的應用程式部署至 Azure App Service。"
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
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>使用 ZIP 檔案將您的應用程式部署至 Azure App Service

本文說明如何使用 ZIP 檔案將您的 Web 應用程式部署到 [Azure App Service](app-service-web-overview.md)。 

此 ZIP 檔案部署所使用的是支援持續整合式部署的同一個 Kudu 服務。 Kudu 為 ZIP 檔案部署支援下列功能： 

- 刪除先前部署所留下的檔案。
- 用來開啟預設建置程序的選項，其中包含套件還原。
- [部署自訂](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)，包括執行中的部署指令碼。  
- 部署記錄。 

如需詳細資訊，請參閱 [Kudu 文件](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

## <a name="prerequisites"></a>先決條件

若要完成本文中的步驟：

* [建立 App Service 應用程式](/azure/app-service/)，或使用您針對另一個教學課程建立的應用程式。

## <a name="create-a-project-zip-file"></a>建立專案 ZIP 檔案

>[!NOTE]
> 如果您在 ZIP 檔案中下載了檔案，請先將檔案解壓縮。 例如，如果您從 GitHub 下載了 ZIP 檔案，則無法以原狀部署該檔案。 GitHub 會新增其他巢狀目錄，其不適用於 App Service。 
>

在本機終端機視窗中，瀏覽至應用程式專案的根目錄。 

此目錄應該包含您的 web 應用程式的項目檔案，例如 index.html、index.php 和 app.js。 也可以包含套件管理檔案，像是 project.json、composer.json、package.json、bower.json 和 requirements.txt。

在專案中建立所有項目的 ZIP 封存。 下列命令會使用您終端機中的預設工具：

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>將 ZIP 檔案上傳至 Cloud Shell

如果您選擇改從本機終端機執行 Azure CLI，請略過此步驟。

遵循此處的步驟，將 ZIP 檔案上傳到 Cloud Shell。 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

如需詳細資訊，請參閱[在 Azure Cloud Shell 中保存檔案](../cloud-shell/persisting-shell-storage.md)。

## <a name="deploy-zip-file"></a>部署 ZIP 檔案

使用 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) 命令，將上傳的 ZIP 檔案部署至 Web 應用程式。 如果您選擇不使用 Cloud Shell，請確定您的 Azure CLI 版本是 2.0.21 或更新版本。 若要查看您有哪個版本，請在本機終端機視窗中執行 `az --version` 命令。 

下列範例會部署您上傳的 ZIP 檔案。 使用 Azure CLI 的本機安裝時，請針對 `--src` 指定您的本機 ZIP 檔案路徑。   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

此命令會將檔案和目錄從 ZIP 檔案部署到預設的 App Service 應用程式資料夾 (`\home\site\wwwroot`)，並重新啟動應用程式。 如果已設定任何其他的自訂建置程序，它也會執行。 如需詳細資訊，請參閱 [Kudu 文件](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。

若要檢視此應用程式的部署清單，您必須使用 REST API (請參閱下一節)。 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>後續步驟

如需更多的進階部署案例，請嘗試[使用 Git 部署至 Azure ](app-service-deploy-local-git.md)。 Git 型部署至 Azure 可啟用版本控制、封裝還原、MSBuild 等等。

## <a name="more-resources"></a>其他資源

* [Kudu：從 zip 檔案部署](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service 部署認證](app-service-deploy-ftp.md)
