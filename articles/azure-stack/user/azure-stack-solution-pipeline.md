---
title: 將您的應用程式部署至 Azure 和 Azure Stack | Microsoft Docs
description: 了解如何使用混合式 CI/CD 管線將應用程式部署至 Azure 和 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 483122f5d2e39c8595d0f28a6b937772c4ea2e50
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258343"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>教學課程：將應用程式部署至 Azure 和 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

混合式持續整合/持續傳遞 (CI/CD) 管線可讓您建置、測試以及將應用程式部署至多個雲端。  在本教學課程中，您將建置範例環境，用以：
 
> [!div class="checklist"]
> * 根據 Visual Studio Team Services (VSTS) 存放庫的程式碼認可起始新的組建。
> * 自動將您新建置的程式碼部署至全域 Azure 以進行使用者驗收測試。
> * 一旦您的程式碼已通過測試，便會自動部署至 Azure Stack。

### <a name="about-the-hybrid-delivery-build-pipe"></a>關於混合式傳遞建置管道

應用程式部署的持續性、安全性和可靠性是您的組織不可或缺的，且對開發小組而言至關重要。 透過混合式 CI/CD 管線，您將可在內部部署環境與公用雲端間整合管線。 您可以直接變更位置而無須切換應用程式。

此方法也可讓您維護一致的開發工具集。 在 Azure 公用雲端與內部部署 Azure Stack 環境間具有一致性的工具，意味著您實作 CI/CD 開發實務的難度將大為降低。 在 Azure 或 Azure Stack 中部署的應用程式和服務具有互通性，且相同的程式碼可在其中任一位置執行，而充分運用內部部署和公用雲端的特性與功能。

深入了解：
 - [什麼是持續整合？](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [什麼是持續傳遞？](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>先決條件

您必須備妥幾項元件，才能建置混合式 CI/CD 管線。 準備這些元件需要一些時間。
 
 - Azure OEM/硬體合作夥伴可部署生產 Azure Stack，而所有使用者均可部署 Azure Stack 開發套件 (ASDK)。 
 - Azure Stack 操作員也必須部署 App Service、建立方案和供應項目、建立租用戶訂用帳戶，以及新增 Windows Server 2016 映像。

如果您已擁有其中部分元件，請在開始之前確定它們符合需求。

本主題也假設您擁有 Azure 和 Azure Stack 的部分知識。 如果您想要深入了解之後再繼續，請務必從這些主題開始：


本教學課程也假設您擁有 Azure 和 Azure Stack 的部分知識。 

若想深入了解之後再繼續，您可以從這些主題開始：
 - [Azure 簡介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

 - 在 Azure 中建立 [Web 應用程式](https://docs.microsoft.com/azure/app-service/app-service-web-overview)。 記下新的 Web 應用程式 URL，因為稍後將會用到。

Azure Stack
 - 使用 Azure Stack 整合系統，或透過下方的連結部署 Azure Stack 開發套件 (ASDK)：
    - 您可以在「[教學課程：使用安裝程式部署 ASDK](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)」中找到關於部署 ASDK 的詳細指示
    - 您可以使用下列 PowerShell 指令碼 [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 )，將許多 ASDK 部署後步驟自動化。

    > [!Note]  
    > ASDK 安裝程序需要七個小時才能完成，因此請據以規劃時段。

 - 將 [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS 服務部署至 Azure Stack。 
 - 在 Azure Stack 環境中建立[方案/供應項目](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)。 
 - 在 Azure Stack 環境中建立[租用戶訂用帳戶](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。 
 - 建立租用戶訂用帳戶中建立 Web 應用程式。 記下新的 Web 應用程式 URL，以供後續使用。
 - 部署 VSTS 虛擬機器 (仍是在租用戶訂用帳戶中)。
 - 需要具有 .NET 3.5 的 Windows Server 2016 VM。 此 VM 將會建置在 Azure Stack 上，作為私用組建代理程式。 

### <a name="developer-tools"></a>開發人員工具

 - 建立 [VSTS 工作區](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。 註冊程序會建立名為 **MyFirstProject** 的專案。
 - [安裝 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 並[登入 VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)。
 - 連線至專案並[在本機複製](https://www.visualstudio.com/docs/git/gitquickstart)。
 
 > [!Note]  
 > 您將需要將適當映像聯合執行的 Azure Stack (Windows Server 和 SQL)，且需要部署 App Service。
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>準備 Visual Studio Team Services 整合所需的私用組建或版本代理程式

### <a name="prerequisites"></a>先決條件

Visual Studio Team Services (VSTS) 會使用服務主體對 Azure Resource Manager 進行驗證。 VSTS 必須處於「參與者」狀態，才能在 Azure Stack 訂用帳戶中佈建資源。

以下是啟用這類驗證時所須設定的概要步驟：

1. 應建立服務主體，或可使用現有的服務主體。
2. 必須建立服務主體的驗證金鑰。
3. 必須透過角色型存取控制來驗證 Azure Stack 訂用帳戶，讓 SPN 成為參與者角色的一部分。
4. 必須使用 Azure Stack 端點和 SPN 資訊在 VSTS 中建立新的服務定義。

### <a name="service-principal-creation"></a>服務主體建立

請參閱[服務主體建立](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)指示以建立服務主體，並選擇 [Web 應用程式/API] 作為 [應用程式類型]。

### <a name="access-key-creation"></a>存取金鑰建立

服務主體需要金鑰才能進行驗證，請依照本節中的步驟產生金鑰。


1. 在 Azure Active Directory 中，從 [應用程式註冊]選取您的應用程式。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  記下 [應用程式識別碼] 的值。 在 VSTS 中設定服務端點時，將會使用該值。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 若要產生驗證金鑰，請選取 [設定]。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 若要產生驗證金鑰，請選取 [金鑰]。
 
    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. 提供金鑰的描述和金鑰的持續時間。 完成時，選取 [儲存]。
 
    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    儲存金鑰之後會顯示金鑰的值。 請複製此值，因為您之後就無法擷取金鑰。 提供**金鑰值**和應用程式識別碼，能夠以應用程式的形式登入。 將金鑰值儲存在應用程式可擷取的地方。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>取得租用戶識別碼

在設定服務端點的過程中，VSTS 需要將**租用戶識別碼**對應至 Azure Stack 戳記已部署到的 AAD 目錄。 請依照本節中的步驟收集租用戶識別碼。

1. 選取 **Azure Active Directory**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. 若要取得租用戶識別碼，請選取 Azure AD 租用戶的 [屬性]。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. 複製 [目錄識別碼]。 這個值是您的租用戶識別碼。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>授與在 Azure Stack 訂用帳戶中部署資源的服務主體權限 

若要存取您的訂用帳戶中的資源，您必須將應用程式指派給角色。 決定哪個角色代表應用程式的正確權限。 若要深入了解可用的角色，請參閱 [RBAC：內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。 較低的範圍層級會繼承較高層級的權限。 例如，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源。

1. 瀏覽至您想要讓應用程式指派至的範圍層級。 例如，若要在訂用帳戶範圍指派角色，請選取 [訂用帳戶]。 您可以改為選取資源群組或資源。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. 選取**訂用帳戶**作為指派應用程式的對象 (資源群組或資源)。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. 選取 [存取控制 (IAM)]。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. 選取 [新增] 。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. 選取您想要將應用程式指派給哪個角色。 下圖顯示**擁有者**角色。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 根據預設，Azure Active Directory 應用程式不會顯示在可用選項中。 若要尋找您的應用程式，您必須在搜尋欄位中**提供名稱**。 請選取它。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. 選取 [儲存] 以完成角色指派。 您在使用者清單中看到應用程式已指派給該範圍的角色。

### <a name="role-based-access-control"></a>角色型存取控制

Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更精細的存取管理。 使用 RBAC，您可以僅授與使用者執行其作業所需的存取權。 如需角色型存取控制的詳細資訊，請參閱[管理對 Azure 訂用帳戶資源的存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)。

### <a name="vsts-agent-pools"></a>VSTS 代理程式集區

您可以將代理程式組織到代理程式集區中，而不要個別管理每個代理程式。 代理程式集區會定義該集區中所有代理程式的共用界限。 在 VSTS 中，代理程式集區的範圍限於 VSTS 帳戶中；因此，您可以在 Team 專案間共用代理程式集區。 如需關於如何建立 VSTS 代理程式集區的詳細資訊和教學課程，請參閱[建立代理程式集區和佇列](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)。

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>新增 Azure Stack 的個人存取權杖 (PAT)

1. 登入您的 VSTS 帳戶，並選取帳戶設定檔名稱。
2. 選取 [管理安全性] 以存取權杖建立頁面。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. 複製權杖。
    
    > [!Note]  
    > 取得權杖資訊。 離開此畫面後將不會再次顯示該資訊。 
    
    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>在裝載 Azure Stack 的組建伺服器上安裝 VSTS 組建代理程式

1.  連線至您在 Azure Stack 主機上部署的組建伺服器。

2.  下載組建代理程式並使用您的個人存取權杖 (PAT) 以服務的形式加以部署，然後以 VM 管理員帳戶執行。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 移至解壓縮的組建代理程式資料夾。 並從更高權限的命令提示字元執行 **run.cmd** 檔案。 

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  run.cmd 完成後，含有解壓縮內容的資料夾應如下所示：

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    您現在可以在 VSTS 資料夾中看到代理程式。

## <a name="endpoint-creation-permissions"></a>端點建立權限

使用者可以建立端點，讓 VSTO 組建能夠將 Azure 服務應用程式部署至堆疊。 VSTS 會連線至組建代理程式，然後再連接 Azure Stack。 

![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. 在 [設定] 功能表上，選取 [安全性]。
2. 在左側的 [VSTS 群組] 清單中，選取 [端點建立者]。 

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. 在 [成員] 索引標籤上，選取 [+ 新增]。 

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. 輸入使用者名稱，然後從清單中選取該使用者。
5. 按一下 [儲存變更] 。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. 在左側的 [VSTS 群組] 清單中，選取 [端點管理員]。
7. 在 [成員] 索引標籤上，選取 [+ 新增]。
8. 輸入使用者名稱，然後從清單中選取該使用者。
9. 按一下 [儲存變更] 。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    Azure Stack 中的組建代理程式會取得來自 VSTS 的指示，然後傳達與 Azure Stack 進行通訊所需的端點資訊。 
    
    VSTS 與 Azure Stack 的連線現已備妥。

## <a name="develop-your-application"></a>開發您的應用程式

設定混合式 CI/CD 以將 Web 應用程式部署至 Azure 和 Azure Stack，並自動將變更推送至這兩個雲端。

> [!Note]  
> 您將需要將適當映像聯合執行的 Azure Stack (Windows Server 和 SQL)，且需要部署 App Service。 檢閱 App Service 文件的「必要條件」一節，以取得 Azure Stack 運算子需求。

### <a name="add-code-to-vsts-project"></a>將程式碼新增至 VSTS 專案

1. 使用在 Azure Stack 上具有專案建立權限的帳戶登入 Visual Studio。

    混合式 CI/CD 可同時套用至應用程式程式碼和基礎結構程式碼。 請這兩個雲端使用 [Azure Resource Manager 範本](https://azure.microsoft.com/resources/templates/) (例如來自 VSTS 的 Web 應用程式程式碼)。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. 建立並開啟預設 Web 應用程式以**複製存放庫**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>為這兩個雲端中的應用程式服務建立獨立的 Web 應用程式部署

1. 編輯 **WebApplication.csproj** 檔案：選取**Runtimeidentifier**，並新增 `win10-x64.`。如需詳細資訊，請參閱[獨立的部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文件。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. 使用 Team Explorer 將程式碼簽入 VSTS 中。

3. 確認應用程式程式碼已簽入 Visual Studio Team Services 中。 

### <a name="create-the-build-definition"></a>建立組建定義

1. 登入 VSTS 以確認能夠建立組建定義。

2. 新增 **-r win10-x64** 程式碼。 這是觸發 .Net Core 的獨立部署時所需的程式碼。 

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. 執行組建。 [獨立的部署組建](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)程序將會發佈可在 Azure 和 Azure Stack 上執行的成品。

### <a name="using-an-azure-hosted-agent"></a>使用 Azure 託管的代理程式

在 VSTS 中使用託管的代理程式，是建置及部署 Web 應用程式的便利選項。 Microsoft Azure 會自動執行維護和升級，以支援持續而不間斷的開發、測試和部署。

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>管理和設定持續部署 (CD) 程序

Visual Studio Team Services (VSTS) 和 Team Foundation Server (TFS) 提供具有高度設定和管理能力的管線，可用於對多個環境的發行 (例如開發、暫存、QA 和生產環境)；包括特定階段需要核准。

### <a name="create-release-definition"></a>建立發行定義

![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. 在 VSO 的 [建置及發行] 頁面的 [發行] 索引標籤下選取 **\[+**，以新增發行。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\102.png)

2. 套用 **Azure App Service 部署**範本。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\103.png)

3. 在 [新增成品] 下拉式功能表中，為 Azure 雲端建置應用程式**新增成品**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\104.png)

4. 在 [管線] 索引標籤下選取 [階段]、環境的 [工作] 連結，並設定 Azure 雲端環境值。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\105.png)

5. 設定 [環境名稱]，並選取 Azure 雲端端點的 Azure**訂用帳戶**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\106.png)

6. 在 [環境名稱] 下設定所需的 **Azure App Service 名稱**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\107.png)

7. 在 Azure 雲端託管環境的代理程式佇列下輸入 **Hosted VS2017**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\108.png)

8. 在 [部署 Azure App Service] 功能表中，為環境選取有效的**套件或資料夾**。 對**資料夾位置**選取 [確定]。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\110.png)

9. 儲存所有變更，並返回**發行管線**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\111.png)

10. 選取 Azure Stack 應用程式的組建，以新增**新成品**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\112.png)

11. 再新增一個套用 **Azure App Service 部署**的環境。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\113.png)

12. 將新環境命名為 **Azure Stack**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\114.png)

13. 在 [工作] 索引標籤下找出 Azure Stack 環境。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\115.png)

14. 選取 Azure Stack 端點的**訂用帳戶**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\116.png)

15. 將 Azure Stack Web 應用程式名稱設定為 **App Service 名稱**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\117.png)

16. 選取 [Azure Stack 代理程式]。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\118.png)

17. 在 [部署 Azure App Service] 區段下，為環境選取有效的**套件或資料夾**。 對**資料夾位置**選取 [確定]。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\120.png)

18. 在 [變數] 索引標籤下新增名為 **VSTS_ARM_REST_IGNORE_SSL_ERRORS** 的變數，並將其值設定為 **true**，範圍設定為 **Azure Stack**。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\121.png)

19. 選取兩個成品中的 [持續] 部署觸發程序圖示，並啟用持續部署觸發程序。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\122.png)

20. 選取 Azure Stack 環境中的 [預先部署] 條件圖示，並將觸發程序設定為 [發行之後]。

21. 儲存所有變更。

> [!Note]  
> 工作的某些設定可能已在您從範本建立發行定義時自動定義為[環境變數](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)。 這些設定無法在工作設定中修改；您必須選取父環境項目才能編輯這些設定。

## <a name="create-a-release"></a>建立發行

現在，您已完成對發行定義的修改，接著即可開始進行部署。 若要這樣做，您可以從發行定義建立發行。 發行可能會建立自動；例如，持續部署觸發程序會設定於發行定義中。 這表示修改原始程式碼將會啟動新的建置，繼而產生新發行。 但在本節中，您將手動建立新發行。

1. 開啟 [發行] 下拉式清單，然後選擇 [建立發行]。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. 輸入發行的說明，確認已選取正確的成品，然後選擇 [建立]。 幾分鐘之後將會出現一個橫幅，指出新的發行已建立。 選擇連結 (發行的名稱)。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. 此時會開啟 [發行摘要] 頁面，顯示發行的詳細資料。 在 [環境] 區段中，您會看到 "QA" 環境的部署狀態從「進行中」變更為「成功」，且此時會出現一個橫幅，指出發行正在等待核准。 對環境的部署若擱置或失敗，將會顯示藍色的 (i) 資訊圖示。 指向該圖示可檢視包含原因的快顯視窗。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\202.png)

其他檢視 (例如發行清單) 也會顯示指出核准擱置中的圖示。 當您指向該圖示時，會隨即顯示一個包含環境名稱和更多詳細資料的快顯視窗。 這可以讓管理員輕鬆查看哪個發行正在等待核准，以及所有發行的整體進度。

### <a name="monitor-and-track-deployments"></a>監視和追蹤部署

在本節中，您將了解如何從您在上一節中建立的發行來監視及追蹤部署 (此範例以兩個 Azure App Service 網站為目標)。

1. 在 [發行摘要] 頁面中，選擇 [記錄] 連結。 在部署進行時，此頁面會顯示來自代理程式的即時記錄，而左窗格則會指出每個環境的部署程序中各項作業的狀態。

    選擇部署前或部署後核准的 [動作] 資料行中的圖示，以查看哪些人核准 (或拒絕) 部署的相關詳細資料，以及該使用者提供的訊息。

2. 部署完成後，整個記錄檔會顯示在右窗格中。 在左窗格中選取任何**處理步驟**，會只顯示該步驟的記錄檔內容。 這可讓您輕鬆追蹤和偵錯整體部署的個別部分。 或者，您可以從頁面中的圖示和連結下載個別記錄檔，或壓縮所有的記錄檔。

    ![Alt 鍵文字](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. 開啟 [摘要] 索引標籤，以查看發行的整體詳細資料。 其中會顯示發行所部署到的組建和環境，以及部署狀態和其他關於發行的資料。

4. 選取個別的**環境連結**，可查看與該環境的現有和擱置部署有關的詳細資料。 您可以使用這些頁面來確認同一個組建已部署至兩個環境。

5. 在您的瀏覽器中開啟**已部署的生產應用程式**。 例如，針對 Azure App Service 網站，請使用 URL `http://[your-app-name].azurewebsites.net`。

## <a name="next-steps"></a>後續步驟

- 若要深入了解 Azure 雲端模式，請參閱[雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns)。
