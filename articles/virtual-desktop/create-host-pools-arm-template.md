---
title: 使用 Azure Resource Manager 範本-Azure 中建立 Windows 虛擬桌面預覽主應用程式集區
description: 如何使用 Azure Resource Manager 範本建立 Windows 虛擬桌面預覽中的主應用程式集區。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: ba98328002cafbcede855b1187881d39f1de8fc5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796421"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立主機集區

主機集區是 Windows 虛擬桌面預覽版租用戶環境中一或多個相同虛擬機器的集合。 每個主機集區都可以包含一個應用程式群組，而使用者可如同在實體桌面上與其互動。

請遵循本節中的指示使用 Microsoft 所提供的 Azure Resource Manager 範本建立 Windows 虛擬桌面租用戶的主應用程式集區。 這篇文章會告訴您如何建立主應用程式集區中 Windows 虛擬桌面、 與 Vm 建立的資源群組中的 Azure 訂用帳戶、 將這些 Vm 加入 AD 網域，以及向 Windows 虛擬桌面的 Vm。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>您需要執行的 Azure Resource Manager 範本

請確定您知道下列事項再執行 Azure Resource Manager 範本：

- 其中是您想要使用的映像的來源。 是從 Azure 映像庫還是自訂？
- 您的網域加入認證。
- 您的 Windows 虛擬桌面認證。

當您使用 Azure Resource Manager 範本建立 Windows 虛擬桌面的主應用程式集區時，您可以從 Azure 資源庫、 受管理的映像或非受控映像來建立虛擬機器。 若要深入了解如何建立 VM 映像，請參閱[準備 Windows VHD 或 VHDX 以上傳至 Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)並[在 Azure 中建立一般化 VM 的受控映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)。

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>執行 Azure Resource Manager 範本來佈建一個新的主應用程式集區

若要開始，請前往[此 GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)。

### <a name="deploy-the-template-to-azure"></a>將範本部署到 Azure

如果您要部署的企業訂用帳戶中，向下捲動並選取**部署至 Azure**，然後略過預先填寫參數根據您的映像來源。

如果您要部署雲端解決方案提供者的訂用帳戶，請遵循下列步驟來部署至 Azure:

1. 向下捲動並以滑鼠右鍵按一下**部署至 Azure**，然後選取**複製連結位置**。
2. 開啟 [記事本] 之類的文字編輯器，並貼上那里的連結。
3. 後面"https://portal.azure.com/「 雜湊標記 （#） 之前輸入 at 符號 (@) 後面加上租用戶網域名稱。 以下是您應該使用的格式範例： https://portal.azure.com/@Contoso.onmicrosoft.com#create/。
4. 具有雲端解決方案提供者訂用帳戶的系統管理員/參與者權限的使用者身分登入 Azure 入口網站。
5. 貼上您複製到文字編輯器，在網址列的連結。

如需有關哪些參數中，您應該輸入您的案例的指引，請參閱 Windows 虛擬桌面[讀我檔案](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)。 讀我檔案也會更新最新的變更。

## <a name="assign-users-to-the-desktop-application-group"></a>將使用者指派給桌面應用程式群組

GitHub 的 Azure Resource Manager 範本完成之後，請在您開始測試您的虛擬機器上的完整的工作階段桌面之前先指派使用者存取權。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

若要將使用者指派至桌面應用程式群組，請開啟 PowerShell 視窗並執行這個指令程式可登入 Windows 虛擬桌面環境：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

接下來，設定要搭配這個指令程式的 Azure Resource Manager 範本中指定的租用戶群組的內容：

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

在那之後，將使用者新增至這個指令程式的桌面應用程式群組：

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

使用者的 UPN 應該符合 Azure Active Directory 中的使用者身分識別 (例如user1@contoso.com)。 如果您想要新增多個使用者，則必須針對每個使用者執行此 Cmdlet。

在您完成這些步驟之後，新增至桌面應用程式群組的使用者可以透過支援的遠端桌面用戶端登入 Windows 虛擬桌面，並看到工作階段桌面的資源。

>[!IMPORTANT]
>為了保護您在 Azure 中的 Windows 虛擬桌面環境，建議您不要在 VM 上開啟輸入連接埠 3389。 Windows 虛擬桌面不需要開啟輸入連接埠 3389 讓使用者存取主機集區的 VM。 如果您為了要進行疑難排解而必須開啟連接埠 3389，建議您使用 [Just-In-Time VM 存取](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time)。