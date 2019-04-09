---
title: 在 Windows 虛擬桌面預覽版中建立租用戶 - Azure
description: 描述如何設定 Azure Active Directory 中的 Windows 虛擬桌面預覽版租用戶。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 1c66b3de9e18cb74c43f20499e4065c7ec7ae5ca
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801669"
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
* Azure 訂用帳戶識別碼

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>將 Azure Active Directory 權限授與給 Windows 虛擬桌面預覽版服務

如果您已經將此 Azure Active Directory 的權限授與給 Windows 虛擬桌面，請略過本節。

將權限授與 Windows 虛擬桌面服務，可讓它查詢 Azure Active Directory 以便進行系統管理和使用者工作。

若要授與服務權限：

1. 開啟瀏覽器並連線到 [Windows 虛擬桌面同意頁面](https://rdweb.wvd.microsoft.com)。
2. 針對 [同意選項] > [伺服器應用程式]，輸入 Azure Active Directory 租用戶名稱或目錄識別碼，然後選取 [提交]。
        若為雲端解決方案提供者客戶，識別碼是合作夥伴入口網站中客戶的 Microsoft 識別碼。 若為企業客戶，識別碼位於 [Azure Active Directory] > [屬性] > [目錄識別碼] 之下。
3. 使用全域管理員帳戶登入 Windows 虛擬桌面同意頁面。 例如，如果您屬於 Contoso 組織，您的帳戶可能是 admin@contoso.com 或 admin@contoso.onmicrosoft.com。  
4. 選取 [接受]。
5. 等候一分鐘。
6. 瀏覽回到 [Windows 虛擬桌面同意頁面](https://rdweb.wvd.microsoft.com)。
7. 移至 [同意選項] > [用戶端應用程式]，輸入相同的 Azure AD 租用戶名稱或目錄識別碼，然後選取 [提交]。
8. 如同步驟 3 一樣，以全域管理員身分登入 Windows 虛擬桌面同意頁面。
9. 選取 [接受]。

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>將 TenantCreator 應用程式角色指派給 Azure Active Directory 租用戶中的使用者

將 TenantCreator 應用程式角色指派給 Azure Active Directory 使用者，可讓該使用者建立與 Azure Active Directory 相關聯的 Windows 虛擬桌面租用戶。 您必須使用全域管理員帳戶來指派 TenantCreator 角色。

若要使用您的全域管理員帳戶指派 TenantCreator 應用程式角色：

1. 開啟瀏覽器，然後使用您的全域管理員帳戶連線到 [Azure Active Directory 入口網站](https://aad.portal.azure.com)。
   - 如果您正在處理多個 Azure AD 租用戶，最佳做法是開啟私人瀏覽器工作階段，然後將 URL 複製並貼入位址中。
2. 選取 [企業應用程式]，搜尋 [Windows 虛擬桌面]。 您會看到您在上一節中同意的兩個應用程式。 在這兩個應用程式中，選取 [Windows 虛擬桌面]。
3. 選取 [使用者和群組]，然後選取 [新增使用者]。
4. 在 [新增指派] 刀鋒視窗中選取 [使用者和群組]。
5. 搜尋您將建立 Windows 虛擬桌面租用戶的使用者帳戶。
   - 為了簡單起見，這可以是全域管理員帳戶。
6. 選取使用者帳戶，按一下 [選取]，然後選取 [指派]。

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

應該以貴組織和租用戶相關的值取代以方括號括住的值。 例如，假設您是 Contoso 組織的 Windows 虛擬桌面 TenantCreator。 您會執行的 Cmdlet 如下所示：

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>後續步驟

建立您的租用戶後，您必須建立主機集區。 若要深入了解主機集區，請繼續進行在 Windows 虛擬桌面中建立主機集區的教學課程。

> [!div class="nextstepaction"]
> [Windows 虛擬桌面主機集區教學課程](./create-host-pools-azure-marketplace.md)
