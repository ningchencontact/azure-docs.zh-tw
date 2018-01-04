---
title: "Zip 推入部署為 Azure 函式 |Microsoft 文件"
description: "您可以使用 Kudu 部署服務的.zip 檔案部署功能來發佈 Azure 函式。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/06/2017
ms.author: glenga
ms.openlocfilehash: faddb73522200f60f18294dc43e8d235943f8bbb
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Zip 推入部署為 Azure 函式 
本文說明如何從.zip （壓縮） 的檔案，將您函式應用程式的專案檔案部署至 Azure。 您了解如何執行推入部署，同時藉由使用 Azure CLI 和使用 REST Api。 

Azure 的函式都會有完整的 Azure 應用程式服務所提供的連續部署與整合選項。 如需詳細資訊，請參閱 [Azure Functions 的持續部署](functions-continuous-deployment.md)。 

在開發期間更快反覆項目，通常很容易部署您的函式應用程式專案檔案，直接從壓縮的.zip 檔案。 此.zip 檔案部署會使用相同的 Kudu 服務該 powers 連續整合為基礎的部署，包括：

+ 刪除較舊的部署中所剩餘的檔案。
+ 部署自訂，包括執行部署指令碼。
+ 部署記錄檔。
+ 在同步函式中的觸發程序[耗用量計劃](functions-scale.md)函式應用程式。

如需詳細資訊，請參閱[.zip 發送部署參考](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)。 

## <a name="deployment-zip-file-requirements"></a>.Zip 檔案的部署需求
您在用於推入部署.zip 檔案必須包含的所有應用程式中函式，包括您的函式程式碼的專案檔案。 

>[!IMPORTANT]
> 當您使用.zip 推入部署時，從現有部署的.zip 檔案中找不到任何檔案會從您的函式應用程式中刪除。  

### <a name="function-app-folder-structure"></a>函式應用程式資料夾結構

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>下載您的函式應用程式專案

當您在本機電腦上開發時，很容易就能建立在開發電腦上的函式的應用程式專案資料夾的.zip 檔案。 

不過，您可能會在 Azure 入口網站中使用編輯器 中建立函式。 若要從入口網站下載函式應用程式專案： 

1. 登入[Azure 入口網站](https://portal.azure.com)，然後移至您的函式應用程式。

2. 在**概觀**索引標籤上，選取**下載應用程式內容**。 選取您的下載選項，然後選取**下載**。     

    ![下載函式應用程式專案](./media/deployment-zip-push/download-project.png)

下載的.zip 檔案是在要重新發佈至您的函式應用程式使用.zip 推入部署的正確格式。

您也可以從 GitHub 儲存機制下載.zip 檔案。 請記住，當您下載為.zip 檔案的 GitHub 儲存機制，GitHub 將分支的額外的資料夾層級。 您無法將.zip 檔案，當您直接部署此額外的資料夾層級表示從 GitHub 下載它。 如果您正在使用 GitHub 儲存機制來維護您的函式應用程式，您應該使用[連續整合](functions-continuous-deployment.md)來部署應用程式。  

## <a name="cli"></a>使用 Azure CLI 部署

您可以使用 Azure CLI 觸發推入部署。 推入部署.zip 檔案至函式應用程式使用[az functionapp 部署來源組態-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip)命令。 若要使用此命令，您必須使用 Azure CLI 版本 2.0.21 或更新版本。 若您使用 Azure CLI 版本，請使用`az --version`命令。

在下列命令，取代`<zip_file_path>`預留位置的.zip 檔案位置的路徑。 此外，取代`<app_name>`函式應用程式的唯一名稱。 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
此命令會將部署到您在 Azure 中的函式應用程式的專案檔從下載的.zip 檔案。 然後重新啟動應用程式。 若要檢視此函式應用程式的部署清單中，您必須使用 REST Api。

當您在本機電腦上使用 Azure CLI`<zip_file_path>`是在您的電腦上的.zip 檔案的路徑。 您也可以執行 Azure CLI [Azure 雲端殼層](../cloud-shell/overview.md)。 當您使用雲端殼層時，必須與您的雲端 Shell 相關聯的 Azure 檔案帳戶的第一次上傳您部署的.zip 檔案。 在此情況下，`<zip_file_path>`雲端殼層帳戶所使用之儲存體位置。 如需詳細資訊，請參閱[保存在 Azure 雲端介面中的檔案](../cloud-shell/persisting-shell-storage.md)。


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的持續部署](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
