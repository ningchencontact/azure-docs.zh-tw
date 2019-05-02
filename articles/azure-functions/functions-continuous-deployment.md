---
title: Azure Functions 的持續部署 | Microsoft Docs
description: 您可以使用 Azure App Service 的持續部署功能來發佈您的函式。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: cb3f3ad3bb7b42429654ea4bf9b49f7e230db1da
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943889"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的持續部署
Azure Functions 可讓您輕鬆地使用持續整合來部署您的函式應用程式。 函式整合了主要的程式碼儲存機制與部署來源。 這項整合可讓您透過這些服務的觸發程序部署的其中一個 Azure 函式程式碼會更新其中的工作流程。 如果您還不熟悉 Azure Functions，以啟動[Azure Functions 概觀](functions-overview.md)。

持續部署在您要在其中整合多個是不錯的選擇，專案以及頻繁參與。 它也可讓您維護您的函式程式碼的原始檔控制。 Azure Functions 支援下列的部署來源：

* [Bitbucket](https://bitbucket.org/)
* [Dropbox](https://www.dropbox.com/)
* 外部存放庫 (Git 或 Mercurial)
* [Git 本機存放庫](../app-service/deploy-local-git.md)
* [GitHub](https://github.com)
* [OneDrive](https://onedrive.live.com/)
* [Azure DevOps](https://azure.microsoft.com/services/devops/)

設定部署時，是依每一函數應用程式進行設定。 當持續部署啟用後，在入口網站中對函式程式碼的存取權會設定為「唯讀」 。

## <a name="requirements-for-continuous-deployment"></a>連續部署的需求

設定持續部署之前，您必須設定部署來源和您的函式程式碼中的部署來源。 在函式應用程式部署中，每個函式是在於具名子目錄，其中的目錄名稱會是函式的名稱。  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

若要能夠從 Azure DevOps 部署，您必須先將 Azure DevOps 組織連結到 Azure 訂用帳戶。 如需詳細資訊，請參閱[設定適用於 Azure DevOps 組織的帳單](https://docs.microsoft.com/azure/devops/organizations/billing/set-up-billing-for-your-organization-vs?view=vsts#set-up-billing-via-the-azure-portal)。

## <a name="set-up-continuous-deployment"></a>設定連續部署
您可以使用此程序來為現有的函數應用程式設定持續部署。 下列步驟示範與 GitHub 存放庫的整合，但類似的步驟也適用於 Azure DevOps 或其他部署服務。

1. 在您函式應用程式中[Azure 入口網站](https://portal.azure.com)，選取**平台功能** > **部署選項**。 
   
    ![開啟 部署選項的選取項目](./media/functions-continuous-deployment/setup-deployment.png)
 
1. 在 **部署**刀鋒視窗中，選取**安裝**。
 
    ![部署 刀鋒視窗](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. 在 **部署來源**刀鋒視窗中，選取**選擇來源**。 填寫您的所選的部署來源的資訊，然後選取**確定**。
   
    ![選擇部署來源](./media/functions-continuous-deployment/choose-deployment-source.png)

設定持續部署之後，部署來源中的所有檔案變更都會都複製到函式應用程式，並觸發完整的網站部署。 當來源中的檔案更新時，便會重新部署網站。

## <a name="deployment-scenarios"></a>部署案例

典型部署案例包括建立預備環境部署，以及將現有的函式移至持續部署。

<a name="staging"></a>
### <a name="create-a-staging-deployment"></a>建立預備部署

函式應用程式不支援部署位置。 但您仍可使用持續整合來管理個別的預備和生產環境部署。

設定及使用預備部署的程序通常應該會像下面這樣︰

1. 您的訂用帳戶中建立兩個函式應用程式： 一個用於實際執行程式碼，一個用於預備。 

1. 建立部署來源 (如果還未擁有)。 此範例使用 [GitHub]。

1. 針對生產函數應用程式，完成上述在[設定持續部署](#set-up-continuous-deployment)中的步驟，並將部署分支設定為 GitHub 存放庫的主要分支。
   
    ![選擇部署分支的選取項目](./media/functions-continuous-deployment/choose-deployment-branch.png)

1. 針對預備函式應用程式，重複步驟 3，但改為在您的 GitHub 存放庫中選擇預備分支。 如果部署來源不支援分支功能，請使用不同的資料夾。
    
1. 對預備分支或資料夾中的程式碼進行更新，然後確認 預備部署會反映這些變更。

1. 經過測試之後，將預備分支的變更合併到主要分支。 這項合併會觸發以函數應用程式為目的地的部署。 如果部署來源不支援分支，請以預備資料夾中的檔案覆寫生產資料夾中的檔案。

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>將現有函式移至持續部署
當您有現有的函式，您已建立並維護在入口網站中，您要下載您的函式程式碼檔案使用 FTP 或本機 Git 存放庫之前，您可以設定持續部署如先前所述。 您可以在 Azure App Service 設定函式應用程式。 下載檔案之後，您可以上載到您所選的連續部署的來源。

> [!NOTE]
> 設定持續整合之後，您可以不會再編輯 Functions 入口網站中的原始程式檔。

<a name="credentials"></a>
#### <a name="configure-deployment-credentials"></a>設定部署認證
您也可以使用 FTP 或本機 Git 存放庫從函數應用程式下載檔案之前，您必須設定您的認證來存取網站。 函式應用程式層級會設定認證。 請使用下列步驟，在 Azure 入口網站中設定部署認證：

1. 在您函式應用程式中[Azure 入口網站](https://portal.azure.com)，選取**平台功能** > **部署認證**。
   
1. 輸入使用者名稱和密碼，然後按**儲存**。 

   ![若要設定本機部署認證的選項](./media/functions-continuous-deployment/setup-deployment-credentials.png)

您現在可以使用這些認證從 FTP 或內建的 Git 儲存機制存取函式應用程式。

<a name="downftp"></a>
#### <a name="download-files-by-using-ftp"></a>使用 FTP 下載檔案

1. 在您函式應用程式中[Azure 入口網站](https://portal.azure.com)，選取**平台功能** > **屬性**。 然後，將複製的值**FTP/部署使用者**， **FTP 主機名稱**，並**FTPS 主機名稱**。  

   [FTP/部署使用者] 中必須輸入入口網站中所顯示的值 (包括應用程式名稱)，以針對 FTP 伺服器提供適當的內容。
   
   ![若要取得您的部署資訊的選取項目](./media/functions-continuous-deployment/get-deployment-credentials.png)

1. 從您的 FTP 用戶端，使用您所蒐集連接到您的應用程式，並下載您的函式的原始程式檔的連接資訊。

<a name="downgit"></a>
#### <a name="download-files-by-using-a-local-git-repository"></a>使用本機 Git 儲存機制下載檔案

1. 在您函式應用程式中[Azure 入口網站](https://portal.azure.com)，選取**平台功能** > **部署選項**。 
   
    ![開啟 部署選項的選取項目](./media/functions-continuous-deployment/setup-deployment.png)
 
1. 接著，在**部署**刀鋒視窗中，選取**安裝**。
 
    ![部署 刀鋒視窗](./media/functions-continuous-deployment/setup-deployment-1.png)
   
1. 在 **部署來源**刀鋒視窗中，選取**本機 Git 儲存機制** > **確定**。

1. 在 **平台功能**，選取**屬性**並記下 Git URL 的值。 
   
    ![取得 Git URL 的選取項目](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

1. 使用 Git 感知命令提示字元或您慣用的 Git 工具複製到本機電腦上的存放庫。 Git 複製命令看起來如下︰
   
        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

1. 將函式應用程式的檔案擷取到本機電腦上的複製，如下列範例所示︰
   
        git pull origin master
   
    如果系統要求，請提供[已設定的部署認證](#credentials)。  

[GitHub]: https://github.com/

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Functions 的最佳做法](functions-best-practices.md)
