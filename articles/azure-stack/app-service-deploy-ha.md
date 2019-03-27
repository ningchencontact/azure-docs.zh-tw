---
title: 以高可用性設定部署 Azure Stack App Service | Microsoft Docs
description: 了解如何使用高可用性設定在 Azure Stack 中部署 App Service。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/13/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: db95be94028fcf16871a9dcfee5f0d87eb5d2cdc
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285661"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>以高可用性設定部署 App Service

此文章說明如何使用 Azure Stack 市集項目，以高可用性設定為 Azure Stack 部署 App Service。 除了可用的市集項目之外，此解決方案還使用 [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入門範本。 此範本會自動建立用於裝載 App Service 資源提供者的高可用性基礎結構。 然後，在此高可用性 VM 基礎結構上安裝 App Service。 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>部署高可用性 App Service 基礎結構 VM
[appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入門範本簡化了高可用性設定中 App Service 的部署。 它應該部署到預設提供者訂用帳戶。 

當用於在 Azure Stack 中建立自訂資源時，此範本會建立：
- 虛擬網路和必要的子網路。
- 檔案伺服器、SQL Server 和 Active Directory Domain Services (AD DS) 子網路的網路安全性群組。
- VM 磁碟和叢集雲端見證的儲存體帳戶。
- 一個用於 SQL VM 且私人 IP 繫結至 SQL AlwaysOn 接聽程式的內部負載平衡器。
- AD DS、檔案伺服器叢集和 SQL 叢集的三個可用性設定組。
- 兩個節點的 SQL 叢集。
- 兩個節點的檔案伺服器叢集。
- 兩個網域控制站。

### <a name="required-azure-stack-marketplace-items"></a>必要的 Azure Stack 市集項目
在使用此範本之前，請確保 Azure Stack 執行個體中有下列 [Azure Stack 市集項目](azure-stack-marketplace-azure-items.md)：

- Windows Server 2016 Datacenter 核心映像 (適用於 AD DS 和檔案伺服器 VM)
- Windows Server 2016 上的 SQL Server 2016 SP2 (Enterprise)
- 最新的 SQL IaaS 擴充功能 
- 最新的 PowerShell 預期狀態設定擴充功能 

> [!TIP]
> 如需範本需求和預設值的其他詳細資料，請檢閱 GitHub上的[範本讀我檔案](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha)。 

### <a name="deploy-the-app-service-infrastructure"></a>部署 App Service 基礎結構
使用此節中的步驟，使用 **appservice-fileshare-sqlserver-ha** Azure Stack 快速入門範本建立自訂部署。

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. 選取 [\+ 建立資源] > [自訂]，然後選取 [範本部署]。

   ![自訂範本部署](media/app-service-deploy-ha/1.png)


3. 在 [自訂部署] 刀鋒視窗上，選取 [編輯範本] > [快速入門範本]，然後使用可用自訂範本的下拉式清單來選取 [appservice-fileshare-sqlserver-ha] 範本，按一下 [確定]，然後按一下 [儲存]。

   ![選取 [appservice-fileshare-sqlserver-ha] 快速入門範本](media/app-service-deploy-ha/2.png)

4. 在 [自訂部署] 刀鋒視窗上，選取 [編輯參數]，然後向下捲動以檢視預設範本值。 視需要修改這些值以提供所有必要的參數資訊，然後按一下 [確定]。<br><br> 至少，為 ADMINPASSWORD、FILESHAREOWNERPASSWORD、FILESHAREUSERPASSWORD、SQLSERVERSERVICEACCOUNTPASSWORD 和 SQLLOGINPASSWORD 參數提供複雜密碼。
    
   ![編輯自訂部署參數](media/app-service-deploy-ha/3.png)

5. 在 [自訂部署] 刀鋒視窗上，確保選取 [預設提供者訂用帳戶] 作為要使用的訂用帳戶，然後建立新資源群組或選取現有的資源群組來進行自訂部署。<br><br> 接著，選取資源群組位置 (如果是 ASDK 安裝，則為 [本機])，然後按一下 [建立]。 在範本部署開始之前，系統將會驗證自訂部署設定。

    ![建立自訂部署](media/app-service-deploy-ha/4.png)

6. 在系統管理入口網站中，選取 [資源群組]，然後選取您針對自訂部署建立的資源群組名稱 (在此範例中是 **app-service-ha**)。 請檢視部署狀態，以確定所有部署皆已順利完成。

   > [!NOTE]
   > 範本部署將需要一小時的時間才能完成。

   [![](media/app-service-deploy-ha/5-sm.png "檢閱範本部署狀態")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>記錄範本輸出
範本部署順利完成後，記錄範本部署輸出。 執行 App Service 安裝程式時，您需要提供此資訊。 

請確實記下這些輸出值：
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

請遵循下列步驟來探索範本輸出值：

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. 在系統管理入口網站中，選取 [資源群組]，然後選取您針對自訂部署建立的資源群組名稱 (在此範例中是 **app-service-ha**)。 

3. 按一下 [部署]，並選取 [Microsoft.Template]。

    ![Microsoft.Template 部署](media/app-service-deploy-ha/6.png)

4. 選取 [Microsoft.Template] 部署後，選取 [輸出] 並記錄範本參數輸出。 部署 App Service 時，會需要此資訊。

    ![參數輸出](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>以高可用性設定部署 App Service
請遵循本節中的步驟，根據 [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack 快速入門範本，以高可用性設定針對 Azure Stack 部署 App Service。 

安裝 App Service 資源提供者之後，您可以將它併入至您的供應項目和方案中。 然後使用者可以訂閱以取得服務，並開始建立應用程式。

> [!IMPORTANT]
> 在執行資源提供者安裝程式之前，請確定您已閱讀每個 App Service 版本附帶的版本資訊，以了解可能影響您的部署的新功能、修正程式，以及任何已知的問題。

### <a name="prerequisites"></a>必要條件
在執行 App Service 安裝程式之前，需要執行幾個步驟，如[開始使用 Azure Stack 上的 App Service 之前](azure-stack-app-service-before-you-get-started.md)一文中所述：

> [!TIP]
> 並非所有在開始使用一文中所述的步驟都是必需的，因為範本部署會為您設定基礎結構 VM。 

- [下載 App Service 安裝程式與協助程式指令碼](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts)。
- [將最新的自訂指令碼擴充功能下載到 Azure Stack 市集](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace)。
- [產生所需的憑證](azure-stack-app-service-before-you-get-started.md#get-certificates)。
- 根據您為 Azure Stack 選擇的身分識別提供者，建立識別碼應用程式。 可以為 [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) 或 [Active Directory 同盟服務](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application)建立識別碼應用程式，並記錄應用程式識別碼。
- 確定您已將 Windows Server 2016 Datacenter 映像新增至 Azure Stack 市集。 這是 App Service 安裝的必要條件。

### <a name="deploy-app-service-in-highly-available-configuration"></a>以高可用性設定部署 App Service
安裝 App Service 資源提供者至少需要一個小時。 所需的時間長度取決於您將部署多少角色執行個體。 在部署期間，安裝程式會執行下列工作：

- 在指定的 Azure Stack 儲存體帳戶中建立 Blob 容器。
- 建立適用於 App Service 的 DNS 區域與項目。
- 註冊 App Service 資源提供者。
- 註冊 App Service 資源庫項目。

若要部署 App Service 資源提供者，請遵循下列步驟：

1. 從可存取「Azure Stack 管理」的「Azure 資源管理」端點電腦，以系統管理員身分執行之前下載的 App Service 安裝程式 (**appservice.exe**)。

2. 選取 [部署 App Service 或升級到最新版本]。

    ![部署或升級](media/app-service-deploy-ha/01.png)

3. 接受 Microsoft 授權條款，然後按一下 [下一步]。

    ![Microsoft 授權條款](media/app-service-deploy-ha/02.png)

4. 接受非 Microsoft 授權條款，然後按一下 [下一步]。

    ![非 Microsoft 授權條款](media/app-service-deploy-ha/03.png)

5. 為您的 Azure Stack 環境提供 App Service 雲端端點設定。

    ![App Service 雲端端點設定](media/app-service-deploy-ha/04.png)

6. **連線**到要用於安裝的 Azure Stack 訂用帳戶，並選擇位置。 

    ![連線到 Azure Stack 訂用帳戶](media/app-service-deploy-ha/05.png)

7. 針對用來部署高可用性範本的資源群組選取 [使用現有的 VNet 和子網路] 以及 [資源群組名稱]。<br><br>接下來，選取作為範本部署的一部分建立的虛擬網路，然後從下拉式清單選項中選取適當的角色子網路。 

    ![Vnet 選取項目](media/app-service-deploy-ha/06.png)

8. 提供先前記錄的檔案共用路徑和檔案共用擁有者參數的範本輸出資訊。 完成後，請按一下 [下一步]。

    ![檔案共用輸出資訊](media/app-service-deploy-ha/07.png)

9. 因為用來安裝 App Service 的機器與用來裝載 App Service 檔案共用的檔案伺服器不在相同的 VNet 上，因此您將無法解析該名稱。 這是預期行為。<br><br>確認為檔案共用 UNC 路徑和帳戶資訊輸入的資訊是否正確，並在 [警示] 對話方塊中按 [是] 以繼續安裝 App Service。

    ![預期的錯誤對話方塊](media/app-service-deploy-ha/08.png)

10. 提供身分識別應用程式識別碼，以及身分識別憑證的路徑和密碼，然後按一下 [下一步]：
    - 身分識別應用程式憑證 (格式為 **sso.appservice.local.azurestack.external.pfx**)
    - Azure Resource Manager 根憑證 (**AzureStackCertificationAuthority.cer**)

    ![識別碼應用程式憑證和根憑證](media/app-service-deploy-ha/008.png)

10. 接下來，提供下列憑證所需的其餘資訊，然後按一下 [下一步]：
    - 預設 Azure Stack SSL 憑證 (格式為 **_.appservice.local.azurestack.external.pfx**)
    - API SSL 憑證 (格式為 **api.appservice.local.azurestack.external.pfx**)
    - 發行者憑證 (格式為 **ftp.appservice.local.azurestack.external.pfx**) 

    ![其他設定憑證](media/app-service-deploy-ha/09.png)

11. 使用高可用性範本部署輸出中的 SQL Server 連接資訊提供 SQL Server 連線資訊：

    ![SQL Server 連線資訊](media/app-service-deploy-ha/10.png)

12. 因為用來安裝 App Service 的機器與用來裝載 App Service 資料庫的 SQL Server 不在相同的 VNet 上，因此您將無法解析該名稱。  這是預期行為。<br><br>確認為 SQL Server 名稱和帳戶資訊輸入的資訊是否正確，並按 [是] 以繼續安裝 App Service。 按 [下一步] 。

    ![SQL Server 連線資訊](media/app-service-deploy-ha/11.png)

13. 接受預設角色設定值或變更為建議值，並按一下 [下一步]。<br><br>針對高可用性設定，我們建議對 App Service 基礎結構角色執行個體的預設值進行變更，如下所示：

    |角色|預設值|高可用性建議|
    |-----|-----|-----|
    |控制器角色|2|2|
    |管理角色|1|3|
    |發佈者角色|1|3|
    |前端角色|1|3|
    |共用背景工作角色|1|10|
    |     |     |     |

    ![基礎結構角色執行個體值](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > 從預設值變更為此教學課程中建議的值，會增加安裝 App Service 的硬體需求。 總共需要 26 個核心和 46,592 MB 的 RAM 以支援建議的 21 個虛擬機器，而不是預設 15 個虛擬機器的 18 個核心和 32,256 MB 的 RAM。

14. 選取要用於安裝 App Service 基礎結構 VM 的平台映像，然後按一下 [下一步]：

    ![平台映像選取項目](media/app-service-deploy-ha/13.png)

15. 提供要使用的 App Service 基礎結構角色認證資訊，然後按一下 [下一步]：

    ![基礎結構角色認證](media/app-service-deploy-ha/14.png)

16. 檢閱用於部署 App Service 的資訊，並按一下 [下一步] 開始部署。 

    ![檢閱安裝摘要](media/app-service-deploy-ha/15.png)

17. 檢閱 App Service 部署進度。 這可能需要超過一小時，具體取決於您的特定部署組態和硬體。 當安裝程式順利完成之後，選取 [結束]。

    ![設定完成](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>後續步驟

[相應放大 App Service](azure-stack-app-service-add-worker-roles.md)。 您可能需要新增其他 App Service 基礎結構背景工作角色，以符合環境中預期的應用程式需求。 根據預設，Azure Stack 上的 App Service 支援免費和共用背景工作角色層。 若要新增其他背景工作角色層，您需要新增更多背景工作角色。

[設定部署來源](azure-stack-app-service-configure-deployment-sources.md)。 需要額外的設定以支援來自多個原始檔控制提供者 (例如 GitHub、BitBucket、OneDrive 和 DropBox) 的依需求部署。
