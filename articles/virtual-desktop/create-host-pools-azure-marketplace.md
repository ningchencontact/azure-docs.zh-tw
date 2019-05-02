---
title: 透過 Azure Marketplace 建立 Windows 虛擬桌面預覽版主機集區 - Azure
description: 如何透過 Azure Marketplace 建立 Windows 虛擬桌面預覽版主機集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: c4ab209d63a01d9e1089eec351eff64fc116f403
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924982"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace"></a>教學課程：透過 Azure Marketplace 建立主機集區

主機集區是 Windows 虛擬桌面預覽版租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

本文說明如何使用 Microsoft Azure Marketplace 供應項目，在 Windows 虛擬桌面租用戶中建立主機集區。 這包括在 Windows 虛擬桌面中建立主機集區、建立內含 Azure 訂用帳戶中 VM 的資源群組、將這些 VM 加入至 Active Directory 網域，以及向 Windows 虛擬桌面註冊 VM。

開始之前，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

## <a name="sign-in-to-azure"></a>登入 Azure

在 <https://portal.azure.com> 登入 Azure 入口網站。

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>執行 Azure Marketplace 供應項目來佈建新的主機集區

若要執行 Azure Marketplace 供應項目來佈建新的主機集區：

1. 選取 **+** 或 [+ 建立資源]。
2. 在 Marketplace 搜尋視窗中輸入 **Windows 虛擬桌面**。
3. 選取 [Windows 虛擬桌面 - 佈建主機集區]，然後選取 [建立]。

請依照指導方針來輸入適當刀鋒視窗的資訊。

### <a name="basics"></a>基本概念

以下是您針對 [基本概念] 刀鋒視窗執行的作業：

1. 輸入主機集區的名稱，該名稱必須是 Windows 虛擬桌面租用戶內的唯一名稱。
2. 為個人桌面選取適當選項。 如果您選取 [是]，系統會將連線到此主機集區的每個使用者永久指派給虛擬機器。
3. 輸入以逗號分隔的使用者清單，這些使用者可以登入 Windows 虛擬桌面用戶端，並在 Azure Marketplace 供應項目完成後存取桌面。 例如，如果您想要指派 user1@contoso.com 和 user2@contoso.com 存取權，請輸入"user1@contoso.com,user2@contoso.com"。
4. 選取 [新建] 並提供新資源群組的名稱。
5. 針對 [位置]，選取與可連線至 Active Directory 伺服器的虛擬網路相同的位置。
6. 選取 [確定] 。

### <a name="configure-virtual-machines"></a>設定虛擬機器

針對 [設定虛擬機器] 刀鋒視窗：

1. 接受預設值，或自訂 VM 的數量或大小。
2. 輸入虛擬機器的名稱前置詞。 比方說，如果您輸入名稱「前置詞」，則虛擬機器會稱為 "prefix-0"、"prefix-1"，依此類推。
3. 選取 [確定] 。

### <a name="virtual-machine-settings"></a>虛擬機器設定

針對 [虛擬機器設定] 刀鋒視窗：

1. 選取 [映像來源] 並輸入如何進行尋找及儲存的相關資訊。 如果您選擇不使用受控磁碟，請選取包含 .vhd 檔案的儲存體帳戶。
2. 針對會將 VM 加入至 Active Directory 網域的網域帳戶，輸入使用者主體名稱和密碼。 此相同使用者名稱和密碼將會建立於虛擬機器作為本機帳戶。 您稍後可以重設這些本機帳戶。
3. 選取可連線至 Active Directory 伺服器的虛擬網路，然後選擇要裝載虛擬機器的子網路。
4. 選取 [確定] 。

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Windows 虛擬桌面預覽版租用戶資訊

針對 Windows 虛擬桌面租用戶資訊刀鋒視窗：

1. 針對包含您租用戶的租用戶群組，輸入 [Windows 虛擬桌面租用戶群組名稱]。 如果您未規劃特定租用戶群組名稱，請將它保留為預設值。
2. 針對您將在其中建立此主機集區的租用戶，輸入 [Windows 虛擬桌面租用戶名稱]。
3. 指定您想要用來以 Windows 虛擬桌面租用戶 RDS 擁有者的身分進行驗證的認證類型。 如果您選取 [服務主體]，則也必須提供與服務主體相關聯的 [Azure AD 租用戶識別碼]。
4. 輸入租用戶管理帳戶的認證。 僅支援具有密碼認證的服務主體。
5. 選取 [確定] 。

## <a name="complete-setup-and-create-the-virtual-machine"></a>完成設定並建立虛擬機器

針對最後兩個刀鋒視窗：

1. 在 [摘要] 刀鋒視窗中，檢閱設定資訊。 如果您需要變更一些資料，請回到適當的刀鋒視窗進行變更，再繼續執行。 如果資訊看起來正確無誤，請選取 [確定]。
2. 在 [購買] 刀鋒視窗中，檢閱有關您從 Azure Marketplace 購買的項目相關資訊。
3. 選取 [建立] 以部署您的主機集區。

根據您要建立多少個 VM，此程序可能需要 30 分鐘或更久時間才能完成。

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(選擇性) 將其他使用者指派給桌面應用程式群組

在 Azure Marketplace 供應項目完成後，您可以先將其他使用者指派給桌面應用程式群組，再於您的虛擬機器上開始測試完整工作階段桌面。 如果您已經在 Azure Marketplace 供應項目中新增預設使用者，而且不想要新增更多使用者，則可略過本節。

若要將使用者指派給桌面應用程式群組，您必須先開啟 PowerShell 視窗。 然後，您必須輸入下列兩個 Cmdlet。

執行下列 Cmdlet 來登入 Windows 虛擬桌面環境：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

使用下列 Cmdlet，將內容設定為您在 Azure Marketplace 供應項目中指定的 Windows 虛擬桌面租用戶群組。 如果您將 Windows 虛擬桌面租用戶群組值保留為 Azure Marketplace 供應項目中的預設值，則可略過此步驟。

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

完成這兩項之後，您可以使用此 Cmdlet 將使用者新增桌面應用程式群組：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

使用者的 UPN 應該符合 Azure Active Directory 中的使用者身分識別 (例如user1@contoso.com)。 如果您想要新增多個使用者，則必須針對每個使用者執行此 Cmdlet。

在您完成這些步驟之後，新增至桌面應用程式群組的使用者可以透過支援的遠端桌面用戶端登入 Windows 虛擬桌面，並看到工作階段桌面的資源。

以下是目前支援的用戶端：

- [Windows 7 和 Windows 10 上的遠端桌面用戶端](connect-windows-7-and-10.md)
- [Windows 虛擬桌面 Web 用戶端](connect-web.md)

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。

## <a name="next-steps"></a>後續步驟

既然您已產生主機集區並指派使用者來存取其桌面，您也可以透過 RemoteApp 填入您的主機集區。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請參閱「管理應用程式群組」教學課程。

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](./manage-app-groups.md)
