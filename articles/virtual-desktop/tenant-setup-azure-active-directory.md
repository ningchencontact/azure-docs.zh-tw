---
title: 在 Windows 虛擬桌面預覽版中建立租用戶 - Azure
description: 描述如何設定 Azure Active Directory 中的 Windows 虛擬桌面預覽版租用戶。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 2c9682746201306f1b99a04462819618225caa11
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66164255"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>教學課程：在 Windows 虛擬桌面預覽版中建立租用戶

在 Windows 虛擬桌面預覽版中建立租用戶是建置桌面虛擬化解決方案的第一個步驟。 租用戶是包含一或多個主機集區的群組。 每個主機集區是由多個工作階段主機所組成，這些主機會在 Azure 中當作虛擬機器執行並向 Windows 虛擬桌面服務註冊。 每個主機集區也是由一或多個應用程式群組所組成，這些群組用來對使用者發佈遠端桌面和遠端應用程式資源。 透過租用戶，您可以建置主機集區、建立應用程式群組、指派給使用者，以及透過服務進行連線。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 將 Azure Active Directory 權限授與給 Windows 虛擬桌面服務。
> * 將 TenantCreator 應用程式角色指派給 Azure Active Directory 租用戶中的使用者。
> * 建立 Windows 虛擬桌面租用戶。

您需要以下各項才能設定 Windows 虛擬桌面租用戶：

* Windows 虛擬桌面使用者的 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 租用戶識別碼。
* Azure Active Directory 租用戶中的全域管理員帳戶。
   * 這也適用於為其客戶建立 Windows 虛擬桌面租用戶的雲端解決方案提供者 (CSP) 組織。 如果您是 CSP 組織，則必須能夠以客戶的 Azure Active Directory 全域管理員身分登入。
   * 系統管理員帳戶必須來自要在其中建立 Windows 虛擬桌面租用戶的 Azure Active Directory 租用戶。 此程序不支援 Azure Active Directory B2B (來賓) 帳戶。
   * 系統管理員帳戶必須是公司或學校帳戶。
* Azure 訂用帳戶

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>將 Azure Active Directory 權限授與給 Windows 虛擬桌面預覽版服務

如果您已經將此 Azure Active Directory 的權限授與給 Windows 虛擬桌面，請略過本節。

將權限授與 Windows 虛擬桌面服務，可讓它查詢 Azure Active Directory 以便進行系統管理和使用者工作。

若要授與服務權限：

1. 開啟瀏覽器並連線到 [Windows 虛擬桌面同意頁面](https://rdweb.wvd.microsoft.com)。
2. 針對 [同意選項]   > [伺服器應用程式]  ，輸入 Azure Active Directory 租用戶名稱或目錄識別碼，然後選取 [提交]  。
        若為雲端解決方案提供者客戶，識別碼是合作夥伴入口網站中客戶的 Microsoft 識別碼。 若為企業客戶，識別碼位於 [Azure Active Directory]   > [屬性]   > [目錄識別碼]  之下。
3. 使用全域管理員帳戶登入 Windows 虛擬桌面同意頁面。 例如，如果您屬於 Contoso 組織，您的帳戶可能是 admin@contoso.com 或 admin@contoso.onmicrosoft.com。  
4. 選取 [接受]  。
5. 等候一分鐘。
6. 瀏覽回到 [Windows 虛擬桌面同意頁面](https://rdweb.wvd.microsoft.com)。
7. 移至 [同意選項]   > [用戶端應用程式]  ，輸入相同的 Azure Active Directory 租用戶名稱或目錄識別碼，然後選取 [提交]  。
8. 如同步驟 3 一樣，以全域管理員身分登入 Windows 虛擬桌面同意頁面。
9. 選取 [接受]  。

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>將 TenantCreator 應用程式角色指派給 Azure Active Directory 租用戶中的使用者

將 TenantCreator 應用程式角色指派給 Azure Active Directory 使用者，可讓該使用者建立與 Azure Active Directory 相關聯的 Windows 虛擬桌面租用戶。 您必須使用全域管理員帳戶來指派 TenantCreator 角色。

若要使用您的全域管理員帳戶指派 TenantCreator 應用程式角色：

1. 開啟瀏覽器，然後使用您的全域管理員帳戶連線到 [Azure 入口網站](https://portal.azure.com)。
   - 如果您正在處理多個 Azure Active Directory 租用戶，最佳做法是開啟私人瀏覽器工作階段，然後將 URL 複製並貼入位址列中。
2. 在 Azure 入口網站的搜尋列中，搜尋**企業應用程式**，然後選取 [服務]  類別底下出現的項目。
3. 在 [企業應用程式]  中，搜尋 [Windows 虛擬桌面]  。 您會看到您在上一節中同意的兩個應用程式。 在這兩個應用程式中，選取 [Windows 虛擬桌面]  。
        ![在 [企業應用程式] 中搜尋「Windows 虛擬桌面」時的搜尋結果螢幕擷取畫面。 已醒目提示名為「Windows 虛擬桌面」的應用程式。](media/tenant-enterprise-app.png)
4. 選取 [使用者和群組]  。 您可能會看到對應用程式授與同意權的系統管理員已列出，並且已獲派**預設存取**角色。 這還不足以建立 Windows 虛擬桌面租用戶。 請繼續遵循這些指示來對使用者新增 **TenantCreator** 角色。
        ![指派來管理「Windows 虛擬桌面」企業應用程式的使用者和群組螢幕擷取畫面。 螢幕擷取畫面顯示只有一個適用於「預設存取」的指派。](media/tenant-default-access.png)
5. 選取 [新增使用者]  ，然後在 [新增指派]  刀鋒視窗中選取 [使用者和群組]  。
6. 搜尋您將建立 Windows 虛擬桌面租用戶的使用者帳戶。 為了簡單起見，這可以是全域管理員帳戶。

    ![選取要新增為 "TenantCreator" 的使用者螢幕擷取畫面。](media/tenant-assign-user.png)

   > [!NOTE]
   > 您必須選取來自此 Azure Active Directory 的使用者 (或包含使用者的群組)。 您無法選擇來賓 (B2B) 使用者或服務主體。

7. 請選取使用者帳戶，然後依序選取 [選取]  按鈕和 [指派]  。
8. 在 [Windows 虛擬桌面 - 使用者和群組]  頁面上，確認您有看到新項目，其中 **TenantCreator** 角色已指派給將要建立 Windows 虛擬桌面租用戶的使用者。
        ![指派來管理「Windows 虛擬桌面」企業應用程式的使用者和群組螢幕擷取畫面。 螢幕擷取畫面現在包含使用者獲派 "TenantCreator" 角色的第二個項目。](media/tenant-tenant-creator-added.png)

在繼續建立您的 Windows 虛擬桌面租用戶之前，您需要兩項資訊：
- 您的 Azure Active Directory 租用戶識別碼 (或**目錄識別碼**)
- 您的 Azure 訂用帳戶 ID

若要尋找您的 Azure Active Directory 租用戶識別碼 (或**目錄識別碼**)：
1. 在相同 Azure 入口網站工作階段的搜尋列中，搜尋**Azure Active Directory**，然後選取 [服務]  類別底下出現的項目。
        ![在 Azure 入口網站中搜尋 "Azure Active Directory" 的結果螢幕擷取畫面。 [服務] 底下的搜尋結果已醒目提示。](media/tenant-search-azure-active-directory.png)
2. 向下捲動，直到您找到**屬性**，然後將其選取。
3. 尋找**目錄識別碼**，然後選取剪貼簿圖示。 將其貼在方便取得的位置，以便稍後用來作為 **AadTenantId**。
        ![Azure Active Directory 屬性的螢幕擷取畫面。 滑鼠停留在 [目錄識別碼] 的剪貼簿圖示上，以便複製及貼上。](media/tenant-directory-id.png)

若要尋找您的 Azure 訂用帳戶識別碼：
1. 在相同 Azure 入口網站工作階段的搜尋列中，搜尋**訂用帳戶**，然後選取 [服務]  類別底下出現的項目。
        ![在 Azure 入口網站中搜尋 "Azure Active Directory" 的結果螢幕擷取畫面。 [服務] 底下的搜尋結果已醒目提示。](media/tenant-search-subscription.png)
2. 選取您想要用來接收 Windows 虛擬桌面服務通知的 Azure 訂用帳戶。
3. 尋找**訂用帳戶識別碼**，然後將滑鼠移到該值上，直到剪貼簿圖示出現。 選取 [剪貼簿] 圖示，然後將其貼在方便取得的位置，以便稍後用來作為 **AzureSubscriptionId**。
        ![Azure 訂用帳戶屬性的螢幕擷取畫面。 滑鼠停留在 [訂用帳戶識別碼] 的剪貼簿圖示上，以便複製及貼上。](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>建立 Windows 虛擬桌面預覽版租用戶

既然您已授與 Windows 虛擬桌面服務查詢 Azure Active Directory 的權限，並將 TenantCreator 角色指派給使用者帳戶，您可以建立 Windows 虛擬桌面租用戶。

首先，[下載並匯入 Windows 虛擬桌面的模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

透過此 Cmdlet，使用 TenantCreator 使用者帳戶登入 Windows 虛擬桌面：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

然後，建立與 Azure Active Directory 租用戶相關聯的新 Windows 虛擬桌面租用戶：

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

應該以貴組織和租用戶相關的值取代以方括號括住的值。 您選擇用於新 Windows 虛擬桌面租用戶的名稱應該要是全域唯一的。 例如，假設您是 Contoso 組織的 Windows 虛擬桌面 TenantCreator。 您會執行的 Cmdlet 如下所示：

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>後續步驟

建立好您的租用戶後，您必須在 Azure Active Directory 中建立服務主體，並對其指派 Windows 虛擬桌面內的角色。 服務主體可讓您順利部署 Windows 虛擬桌面的 Azure Marketplace 供應項目，進而建立主機集區。 若要深入了解主機集區，請繼續進行在 Windows 虛擬桌面中建立主機集區的教學課程。

> [!div class="nextstepaction"]
> [使用 PowerShell 建立服務主體和角色指派](./create-service-principal-role-powershell.md)
