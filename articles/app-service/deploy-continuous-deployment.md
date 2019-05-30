---
title: 持續部署至 - Azure App Service | Microsoft Docs
description: 了解如何啟用持續部署至 Azure App Service。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 384f709bb32f973efec39518eaa895e25136fe23
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390643"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持續部署至 Azure App Service
本文將示範如何設定 [Azure App Service](overview.md) 的持續部署。 App Service 可從 BitBucket、GitHub 及 [Azure DevOps Services](https://www.visualstudio.com/team-services/) 進行持續部署，方法是從這些服務之一的現有存放庫中提取最新更新。

若要了解如何從 Azure 入口網站未列出的雲端存放庫中手動設定持續部署 (例如 [GitLab](https://gitlab.com/))，請參閱[使用手動步驟設定持續部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

將備妥的存放庫發佈至其中一個支援的服務。 如需將專案發佈至這些服務的詳細資訊，請參閱[建立儲存機制 (GitHub)]、[建立儲存機制 (BitBucket)]及[開始使用 Azure DevOps Services]。

## <a name="deploy-continuously-from-github"></a>從 GitHub 持續部署

若要啟用透過 GitHub 的持續部署，請巡覽至 [Azure 入口網站](https://portal.azure.com)中的 App Service 應用程式頁面。

在左側功能表中，按一下 [部署中心]   > [GitHub]   > [授權]  。 請遵循授權提示。 

![](media/app-service-continuous-deployment/github-choose-source.png)

您只需要授權 GitHub 一次。 如果您已獲授權，只需按一下 [繼續]  即可。 您可以按一下 [變更帳戶]  來變更授權的 GitHub 帳戶。

![](media/app-service-continuous-deployment/github-continue.png)

在 [組建提供者]  頁面上選擇組建提供者，然後按一下 [繼續]  。

### <a name="option-1-use-app-service-kudu-build-server"></a>選項 1：使用 App Service Kudu 組建伺服器

在 [設定]  頁面上，選取您要從中持續部署的組織、存放庫與分支。 完成後，按一下 [繼續]  。

若要從儲存機制的 GitHub 組織中部署，請瀏覽至 GitHub 並移至**設定** > **應用程式** > **授權 OAuth 應用程式**. 然後按一下 「 Azure 應用程式服務 」。

![設定 > 應用程式 > 授權 OAuth 應用程式 > Azure App Service](media/app-service-continuous-deployment/github-settings-navigation.png)

在下一步 頁面中，授與應用程式服務存取您的組織存放庫按一下右手邊的 「 授與 」 按鈕。

![按一下 授與應用程式服務存取權的組織儲存機制的 「 授與 」](media/app-service-continuous-deployment/grant-access.png)

您的組織現在應該會在 「 組織 」 清單中顯示**設定**部署中心的頁面。

### <a name="option-2-use-azure-pipelines-preview"></a>選項 2：使用 Azure Pipelines (預覽)

> [!NOTE]
> 若要讓 App Service 在 Azure DevOps Services 組織中建立必要的 Azure Pipelines，您的 Azure 帳戶必須具備 Azure 訂用帳戶的**擁有者**角色。
>

在 [設定]  頁面的 [程式碼]  區段中，選取您要從中持續部署的組織、存放庫與分支。 完成後，按一下 [繼續]  。

在 [設定]  頁面的 [建置]  區段中，設定新的 Azure DevOps Services 組織或指定現有的組織。 完成後，按一下 [繼續]  。

> [!NOTE]
> 如果您想要使用未列出的現有 Azure DevOps Services 組織，則必須[將 Azure DevOps Services 組織連結至 Azure 訂用帳戶](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

在 [測試]  頁面上，選擇是否要啟用負載測試，然後按一下 [繼續]  。

視 App Service 方案的[定價層](https://azure.microsoft.com/pricing/details/app-service/plans/)而定，您可能也會看到 [部署至預備環境]  頁面。 選擇是否要[啟用部署位置](deploy-staging-slots.md)，然後按一下 [繼續]  。

### <a name="finish-configuration"></a>完成設定

在 [摘要]  頁面上確認您的選項，然後按一下 [完成]  。

完成設定時，所選取存放庫中的新認可會持續部署到 App Service 應用程式。

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>從 BitBucket 持續部署

若要啟用透過 BitBucket 的持續部署，請巡覽至 [Azure 入口網站](https://portal.azure.com)中的 App Service 應用程式頁面。

在左側功能表中，按一下 [部署中心]   > [BitBucket]   > [授權]  。 請遵循授權提示。 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

您只需要授權 BitBucket 一次。 如果您已獲授權，只需按一下 [繼續]  即可。 您可以按一下 [變更帳戶]  來變更授權的 BitBucket 帳戶。

![](media/app-service-continuous-deployment/bitbucket-continue.png)

在 [設定]  頁面上，選取您要從中持續部署的存放庫與分支。 完成後，按一下 [繼續]  。

在 [摘要]  頁面上確認您的選項，然後按一下 [完成]  。

完成設定時，所選取存放庫中的新認可會持續部署到 App Service 應用程式。

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>從 Azure Repos (DevOps Services) 持續部署

若要啟用從 [Azure Repos](https://docs.microsoft.com/azure/devops/repos/index) 的持續部署，請巡覽至 [Azure 入口網站](https://portal.azure.com)中的 App Service 應用程式頁面。

在左側功能表中，按一下 [部署中心]   > [Azure Repos]   > [繼續]  。 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

在 [組建提供者]  頁面上選擇組建提供者，然後按一下 [繼續]  。

> [!NOTE]
> 如果您想要使用未列出的現有 Azure DevOps Services 組織，則必須[將 Azure DevOps Services 組織連結至 Azure 訂用帳戶](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

### <a name="option-1-use-app-service-kudu-build-server"></a>選項 1：使用 App Service Kudu 組建伺服器

在 [設定]  頁面上，選取您要從中持續部署的 Azure DevOps Services 組織、專案、存放庫與分支。 完成後，按一下 [繼續]  。

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>選項 2：使用 Azure DevOps Services 持續傳遞

> [!NOTE]
> 若要讓 App Service 在 Azure DevOps Services 組織中建立必要的 Azure Pipelines，您的 Azure 帳戶必須具備 Azure 訂用帳戶的**擁有者**角色。
>

在 [設定]  頁面的 [程式碼]  區段中，選取您要從中持續部署的 Azure DevOps Services 組織、專案、存放庫與分支。 完成後，按一下 [繼續]  。

在 [設定]  頁面的 [建置]  區段中，指定 Azure DevOps Services 應該用來為選取的存放庫執行建置工作的語言架構。 完成後，按一下 [繼續]  。

在 [測試]  頁面上，選擇是否要啟用負載測試，然後按一下 [繼續]  。

視 App Service 方案的[定價層](https://azure.microsoft.com/pricing/details/app-service/plans/)而定，您可能也會看到 [部署至預備環境]  頁面。 選擇是否要[啟用部署位置](deploy-staging-slots.md)，然後按一下 [繼續]  。 DevOps 不允許持續傳遞至生產位置。 這是設計來防止意外部署到生產環境。 您應該設定持續傳遞至預備位置、 驗證變更，並交換位置，當您準備好。

### <a name="finish-configuration"></a>完成設定

在 [摘要]  頁面上確認您的選項，然後按一下 [完成]  。

完成設定時，所選取存放庫中的新認可會持續部署到 App Service 應用程式。

## <a name="disable-continuous-deployment"></a>停用連續部署

若要停用持續部署，請巡覽至 [Azure 入口網站](https://portal.azure.com)中的 App Service 應用程式頁面。

在左側功能表中，按一下 [部署中心]   > [GitHub]  或 [Azure DevOps Services]  或 [BitBucket]   > [中斷連線]  。

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>其他資源

* [如何調查連續部署的常見問題](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [如何使用適用於 Azure 的 PowerShell]
* [Git 文件]
* [專案 Kudu](https://github.com/projectkudu/kudu/wiki)
* [使用 Azure 自動產生 CI/CD 管線，以部署 ASP.NET 4 應用程式](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[如何使用適用於 Azure 的 PowerShell]: /powershell/azureps-cmdlets-docs
[Git 文件]: https://git-scm.com/documentation

[建立儲存機制 (GitHub)]: https://help.github.com/articles/create-a-repo
[建立儲存機制 (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[開始使用 Azure DevOps Services]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
