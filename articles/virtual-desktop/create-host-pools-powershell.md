---
title: 使用 PowerShell-Azure 中建立 Windows 虛擬桌面預覽主應用程式集區
description: 如何使用 PowerShell cmdlet 建立 Windows 虛擬桌面預覽中的主應用程式集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: helohr
ms.openlocfilehash: a58e059e800b13d01ba8e50880bd75077d4418ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833958"
---
# <a name="create-a-host-pool-with-powershell"></a>使用 PowerShell 建立主機集區

主機集區是 Windows 虛擬桌面預覽版租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用您的 PowerShell 用戶端建立主應用程式集區

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

執行下列 cmdlet 來登入 Windows 虛擬桌面環境

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

接下來，執行這個指令程式可在您的 Windows 虛擬桌面租用戶中建立新的主應用程式集區：

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

執行下一個 cmdlet 來建立註冊權杖若要加入的主應用程式集區，並將它儲存到本機電腦上的新檔案的工作階段主機。 您可以指定多久註冊權杖的有效使用-ExpirationHours 參數。

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

之後，執行這個指令程式可將 Azure Active Directory 使用者新增至主應用程式集區的預設傳統型應用程式群組。

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**新增 RdsAppGroupUser** cmdlet 不支援新增安全性群組，並只加入一位使用者每次應用程式群組。 如果您想要將多個使用者新增至應用程式群組，請重新執行的 cmdlet 搭配適當的使用者主體名稱。

執行下列 cmdlet 來匯出給變數，您將在稍後使用的註冊權杖[註冊到 Windows 虛擬桌面的主應用程式集區的虛擬機器](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool)。

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>建立主應用程式集區的虛擬機器

現在您可以建立 Azure 虛擬機器可以加入您的 Windows 虛擬桌面主應用程式集區。

您可以透過多種方式來建立虛擬機器：

- [從 Azure 映像庫映像建立虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [從受控映像建立虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [從非受控映像建立虛擬機器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Windows 虛擬桌面預覽代理程式安裝準備虛擬機器

您需要執行下列動作來準備您的虛擬機器，您才能安裝 Windows 的虛擬桌面代理程式，並向您的 Windows 虛擬桌面主應用程式集區中的虛擬機器：

- 您必須將機器加入網域的功能。 這可讓連入的 Windows 虛擬桌面使用者從他們的 Azure Active Directory 帳戶對應到其 Active Directory 帳戶，而且成功地允許存取虛擬機器。
- 如果虛擬機器正在執行 Windows Server 作業系統，您必須安裝 「 遠端桌面工作階段主機 (RDSH) 角色。 RDSH 角色可讓 Windows 虛擬桌面代理程式，才能順利安裝。

若要成功加入網域，執行每部虛擬機器上的下列事項：

1. [連接到虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)時建立虛擬機器所提供的認證。
2. 在虛擬機器上啟動**控制台中**，然後選取**系統**。
3. 選取 **電腦名稱**，選取**變更設定**，然後選取 **變更...**
4. 選取 **網域**，然後輸入虛擬網路上的 Active Directory 網域。
5. 驗證程式的電腦加入網域的權限的網域帳戶。

    >[!NOTE]
    > 如果您要將 VM 加入 Azure AD Domain Services 環境，請確定您的網域加入使用者也是 [AAD DC 系統管理員群組](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group)的成員。

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>註冊至 Windows 虛擬桌面預覽主應用程式集區的虛擬機器

註冊至 Windows 虛擬桌面主應用程式集區的虛擬機器只需要安裝 Windows 的虛擬桌面代理程式。

若要註冊的 Windows 虛擬桌面代理程式，依下列方式在每個虛擬機器上：

1. [連接到虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine)時建立虛擬機器所提供的認證。
2. 下載並安裝 Windows 虛擬桌面代理程式。
   - 下載[Windows 虛擬桌面代理程式](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv)。
   - 以滑鼠右鍵按一下 下載的安裝程式中，選取**屬性**，選取**解除封鎖**，然後選取**確定**。 這可讓您信任安裝程式的系統。
   - 執行安裝程式。 當安裝程式會要求您註冊權杖時，輸入的值所得**匯出 RdsRegistrationInfo** cmdlet。
3. 下載並安裝 Windows 虛擬桌面代理程式開機載入器。
   - 下載[Windows 虛擬桌面的代理程式的開機載入器](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH)。
   - 以滑鼠右鍵按一下 下載的安裝程式中，選取**屬性**，選取**解除封鎖**，然後選取**確定**。 這可讓您信任安裝程式的系統。
   - 執行安裝程式。

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。

## <a name="next-steps"></a>後續步驟

既然您已擬定主應用程式集區，您可以將它填入 Remoteapp。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請參閱「管理應用程式群組」教學課程。

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](./manage-app-groups.md)
