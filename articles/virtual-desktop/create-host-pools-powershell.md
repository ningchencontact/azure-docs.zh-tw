---
title: 使用 PowerShell-Azure 中建立 Windows 虛擬桌面預覽主應用程式集區
description: 如何使用 PowerShell cmdlet 建立 Windows 虛擬桌面預覽中的主應用程式集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 7b9b7ffbb034a7fb1256d9cc44048cfa55b02245
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402723"
---
# <a name="create-a-host-pool-with-powershell"></a>使用 PowerShell 建立主應用程式集區

主應用程式集區是一或多個相同的虛擬機器在 Windows 虛擬桌面預覽租用戶環境中的集合。 每個主應用程式集區可以包含實體的桌上型電腦上，使用者可以與互動的應用程式群組。

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>使用您的 PowerShell 用戶端建立主應用程式集區

首先，[下載並匯入的 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)來使用您的 PowerShell 工作階段中，如果您還沒有這麼做。

執行下列 cmdlet 來登入 Windows 虛擬桌面環境

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

之後，執行下列 cmdlet 來設定您的租用戶群組的內容。 如果您還沒有租用戶群組的名稱，您的租用戶是在 「 預設租用戶群組中，「 最有可能因此您可以略過這個指令程式。

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
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

執行下列 cmdlet 來匯出給變數，您將在稍後使用的註冊權杖[註冊到 Windows 虛擬桌面的主應用程式集區的虛擬機器](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)。

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
4. 安裝或啟用 Windows 的虛擬桌面並排顯示堆疊。 步驟將會不同虛擬機器使用的 OS 版本而定。
   - 如果您的虛擬機器的作業系統是 Windows Server 2016:
     - 下載[Windows 的虛擬桌面並排顯示堆疊](https://go.microsoft.com/fwlink/?linkid=2084270)。
     - 以滑鼠右鍵按一下 下載的安裝程式中，選取**屬性**，選取**解除封鎖**，然後選取**確定**。 這可讓您信任安裝程式的系統。
     - 執行安裝程式。
   - 如果您的虛擬機器的作業系統是 Windows 10 1809年或更新版本或 Windows Server 2019 或更新版本：
     - 下載[指令碼](https://go.microsoft.com/fwlink/?linkid=2084268)啟用並排顯示堆疊。
     - 以滑鼠右鍵按一下 下載的指令碼中，選取**屬性**，選取**解除封鎖**，然後選取**確定**。 這可讓您信任的指令碼的系統。
     - 從**開始** 功能表中，搜尋 Windows PowerShell ISE 中，按一下滑鼠右鍵，然後選取**系統管理員身分執行**。
     - 選取 **檔案**，然後**開啟...**，然後尋找 PowerShell 指令碼從下載的檔案，並開啟它。
     - 選取綠色的播放按鈕，以執行指令碼。

## <a name="next-steps"></a>後續步驟

既然您已擬定主應用程式集區，您可以將它填入 Remoteapp。 若要深入了解如何管理 Windows 虛擬桌面中的應用程式，請參閱管理應用程式群組教學課程。

> [!div class="nextstepaction"]
> [管理應用程式群組教學課程](./manage-app-groups.md)
