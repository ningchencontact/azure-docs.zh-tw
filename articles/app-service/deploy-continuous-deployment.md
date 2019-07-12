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
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836740"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持續部署至 Azure App Service

[Azure App Service](overview.md)可讓從 GitHub、 BitBucket 持續部署並[Azure 存放庫](https://azure.microsoft.com/services/devops/repos/)儲存機制，藉由提取最新的更新。 這篇文章會示範如何使用 Azure 入口網站來持續部署您的應用程式，透過 Kudu 組建服務或[Azure 管線](https://azure.microsoft.com/services/devops/pipelines/)。 

如需有關原始檔控制服務的詳細資訊，請參閱[建立儲存機制 (GitHub)]，[建立儲存機制 (BitBucket)]，或[建立新的 Git 存放庫 （Azure 儲存機制）]。

若要手動設定持續部署，從入口網站並不直接支援，例如雲端儲存機制[GitLab](https://gitlab.com/)，請參閱[設定使用手動步驟的持續部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>授權 Azure App Service 

若要使用 Azure 儲存機制，請確定您 Azure DevOps 的組織連結到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱 <<c0> [ 設定 Azure DevOps 服務帳戶，讓它可以部署至 web 應用程式](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

針對 Bitbucket 或 GitHub，授權 Azure App Service，以連接到您的儲存機制。 您只需要向原始檔控制服務進行一次授權。 

1. 選取 **應用程式服務**中[Azure 入口網站](https://portal.azure.com)左導覽中，，，然後選取您想要部署的 web 應用程式。 
   
1. 在應用程式 頁面上，選取**部署中心**左側功能表中。
   
1. 在上**部署中心**頁面上，選取**GitHub**或是**Bitbucket**，然後選取**授權**。 
   
   ![選取原始檔控制服務，然後選取 授權。](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 登入服務，如有必要，並遵循授權提示。 

## <a name="enable-continuous-deployment"></a>啟用持續部署 

授權的原始檔控制服務之後，設定您的應用程式，透過內建持續部署[Kudu App Service 部署組建伺服器](#option-1-use-the-app-service-build-service)，或透過[Azure 管線](#option-2-use-azure-pipelines)。 

### <a name="option-1-use-the-app-service-build-service"></a>選項 1：使用 App Service 建置服務

您可以使用內建的 Kudu App Service 建置持續部署的伺服器，從 GitHub、 Bitbucket 或 Azure 儲存機制。 

1. 選取 **應用程式服務**中[Azure 入口網站](https://portal.azure.com)左導覽中，，，然後選取您想要部署的 web 應用程式。 
   
1. 在應用程式 頁面上，選取**部署中心**左側功能表中。
   
1. 在 選取您已獲授權的原始檔控制提供者**部署中心**頁面，然後選取**繼續**。 GitHub 或 Bitbucket，您也可以選取**變更帳戶**變更授權的帳戶。 
   
   > [!NOTE]
   > 若要使用 Azure 儲存機制，請確定您的 Azure DevOps 服務組織會連結到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱 <<c0> [ 設定 Azure DevOps 服務帳戶，讓它可以部署至 web 應用程式](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。
   
1. GitHub 或 Azure 儲存機制，在**組建提供者**頁面上，選取**App Service 建置服務**，然後選取**繼續**。 Bitbucket 一律會使用 App Service 建置服務。
   
   ![選取 App Service 建置服務，然後選取 [繼續]。](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 在 **設定**頁面：
   
   - Github，請在下拉式清單，然後選取**組織**，**存放庫**，並**分支**您想要持續部署。
     
     > [!NOTE]
     > 如果您沒有看到任何存放庫，您可能需要在 GitHub 中的 Azure App Service 的授權。 瀏覽至您的 GitHub 存放庫，並移至**設定** > **應用程式** > **授權 OAuth 應用程式**。 選取  **Azure App Service**，然後選取**授與**。
     
   - 針對 Bitbucket 中，選取 Bitbucket **Team**，**存放庫**，並**分支**您想要持續部署。
     
   - 針對 Azure 儲存機制中，選取**Azure DevOps 組織**，**專案**，**存放庫**，以及**分支**您想要持續部署。
     
     > [!NOTE]
     > 如果未列出您 Azure DevOps 的組織，請確定它會連結至您的 Azure 訂用帳戶。 如需詳細資訊，請參閱 <<c0> [ 設定 Azure DevOps 服務帳戶，讓它可以部署至 web 應用程式](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)...
     
1. 選取 **繼續**。
   
   ![填入存放庫的詳細資訊，然後選取 [繼續]。](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 設定組建提供者之後，檢閱設定，在**摘要**頁面，然後再選取**完成**。
   
   新的認可，在選取的存放庫和分支現在會到 App Service 應用程式持續部署。 您可以在追蹤的認可和部署**部署中心**頁面。
   
   ![追蹤認可和部署中心的部署](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>選項 2：使用 Azure Pipelines 

如果您的帳戶具有必要的權限，您可以設定從 GitHub 或 Azure 儲存機制的存放庫持續部署至 Azure 管線。 如需透過 Azure 管線部署的詳細資訊，請參閱[將 web 應用程式部署至 Azure App Service](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)。

若要建立 Azure DevOps 組織中的持續傳遞 Azure 管線的 Azure App service: 

- 您的 Azure 帳戶必須具有寫入權限加入 Azure Active Directory 建立服務。 
  
- 您的 Azure 帳戶必須擁有**擁有者**您 Azure 訂用帳戶中的角色。

- 您必須是系統管理員可以在您想要使用 Azure DevOps 專案。

若要設定 Azure 管線 （預覽）：

1. 選取 **應用程式服務**中[Azure 入口網站](https://portal.azure.com)左導覽中，，，然後選取您想要部署的 web 應用程式。 
   
1. 在應用程式 頁面上，選取**部署中心**左側功能表中。
   
1. 在 **組建提供者**頁面上，選取**Azure 管線 （預覽）** ，然後選取**繼續**。 
   
1. 在 **設定**頁面上，於**程式碼**區段：
   
   - Github，請在下拉式清單，然後選取**組織**，**存放庫**，並**分支**您想要持續部署。
     
     > [!NOTE]
     > 如果您沒有看到任何存放庫，您可能需要在 GitHub 中的 Azure App Service 的授權。 瀏覽至您的 GitHub 存放庫，並移至**設定** > **應用程式** > **授權 OAuth 應用程式**。 選取  **Azure App Service**，然後選取**授與**。
     
   - 針對 Azure 儲存機制中，選取**Azure DevOps 組織**，**專案**，**存放庫**，以及**分支**您想要持續部署或者，設定新的 Azure DevOps 組織。
     
     > [!NOTE]
     > 如果未列出您現有的 Azure DevOps 組織，您可能需要將它連結到您的 Azure 訂用帳戶。 如需詳細資訊，請參閱 <<c0> [ 定義您的 CD 發行管線](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。
     
1. 選取 **繼續**。
   
1. 針對 Azure 儲存機制，在**建置**區段中，指定語言架構，Azure 管線應該用來執行建置工作，然後選取**繼續**。
   
1. 在 **測試**頁面上，選擇是否要啟用的負載測試，然後選取**繼續**。
   
1. 根據您的 App Service 方案[定價層](https://azure.microsoft.com/pricing/details/app-service/plans/)，您可能會看到**部署至預備環境**頁面。 選擇是否要[啟用部署位置](deploy-staging-slots.md)，然後選取**繼續**。
   
   > [!NOTE]
   > Azure 的管線不允許連續傳遞至生產位置。 這項限制可避免意外部署到生產環境。 設定持續傳遞至預備位置、 驗證變更，和當您準備好，然後交換位置。
   
1. 設定組建提供者之後，檢閱設定，在**摘要**頁面，然後再選取**完成**。
   
   新的認可，在選取的存放庫和分支現在會到 App Service 應用程式持續部署。 您可以在追蹤的認可和部署**部署中心**頁面。
   
   ![追蹤認可和部署中心的部署](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>停用連續部署

若要停用持續部署，請選取**中斷連線**在您的應用程式頂端**部署中心**頁面。

![停用連續部署](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>其他資源

* [調查持續部署的常見問題](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [使用 Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Git 文件](https://git-scm.com/documentation)
* [專案 Kudu](https://github.com/projectkudu/kudu/wiki)

[建立儲存機制 (GitHub)]: https://help.github.com/articles/create-a-repo
[建立儲存機制 (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[建立新的 Git 存放庫 （Azure 儲存機制）]: /azure/devops/repos/git/creatingrepo
