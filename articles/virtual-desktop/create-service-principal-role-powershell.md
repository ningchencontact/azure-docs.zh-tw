---
title: 使用 PowerShell 建立 Windows 虛擬桌面預覽版服務主體和角色指派 - Azure
description: 如何在 Windows 虛擬桌面預覽版中使用 PowerShell 建立服務主體及指派角色。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 44c823653ecbad1c4dd1fd35b676c8a6d8bd1620
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206650"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>教學課程：使用 PowerShell 建立服務主體和角色指派

服務主體是您可以在 Azure Active Directory 中建立的身分識別，用以指派特定用途的角色和權限。 在 Windows 虛擬桌面預覽版中，您可以建立服務主體：

- 將特定 Windows 虛擬桌面管理工作自動化。
- 執行任何 Windows 虛擬桌面 Azure Resource Manager 範本時，使用認證代替需要 MFA 的使用者。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 請在 Azure Active Directory 中建立服務主體。
> * 在 Windows 虛擬桌面中建立角色指派。
> * 使用服務主體登入 Windows 虛擬桌面。

## <a name="prerequisites"></a>必要條件

您必須先執行下列三項作業，才能建立服務主體和角色指派：

1. 安裝 AzureAD 模組。 若要安裝此模組，請以系統管理員身分執行 PowerShell 並執行下列 Cmdlet：

    ```powershell
    Install-Module AzureAD
    ```

2. 使用引號中由您工作階段相關值所取代的值執行下列 Cmdlet。

    ```powershell
    $myTenantName = "<my-tenant-name>"
    ```

3. 在相同的 PowerShell 工作階段中，遵循本文中的所有指示。 如果您關閉視窗並於稍後返回，則可能無法運作。

## <a name="create-a-service-principal-in-azure-active-directory"></a>在 Azure Active Directory 中建立服務主體

在您滿足 PowerShell 工作階段的必要條件後，請執行下列 PowerShell Cmdlet 以在 Azure 中建立多租用戶的服務主體。

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>在 Windows 虛擬桌面預覽版中建立角色指派

既然您已建立服務主體，即可使用它來登入 Windows 虛擬桌面。 請務必使用有權限建立角色指派的帳戶登入。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

執行下列 PowerShell Cmdlet 來連線到 Windows 虛擬桌面，並建立服務主體的角色指派。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>使用服務主體來登入

建立服務主體的角色指派之後，請藉由執行下列 Cmdlet 來確定服務主體可以登入 Windows 虛擬桌面：

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

登入之後，請使用服務主體測試一些 Windows 虛擬桌面 PowerShell Cmdlet 以確定一切運作正常。

## <a name="view-your-credentials-in-powershell"></a>在 PowerShell 中檢視您的認證

結束您的 PowerShell 工作階段之前，請檢視您的認證並記下它們以供日後參考。 密碼特別重要，因為在關閉此 PowerShell 工作階段之後，便無法擷取它。

以下是您應該記下的三個認證，以及您要取得認證所需執行的 Cmdlet：

- 密碼：

    ```powershell
    $svcPrincipalCreds.Value
    ```

- 租用戶識別碼：

    ```powershell
    $aadContext.TenantId.Guid
    ```

- 應用程式識別碼：

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>後續步驟

建立服務主體並將 Windows 虛擬桌面租用戶中的角色指派給租用戶之後，您就可以使用此租用戶建立主機集區。 若要深入了解主機集區，請繼續進行在 Windows 虛擬桌面中建立主機集區的教學課程。

 > [!div class="nextstepaction"]
 > [Windows 虛擬桌面主機集區教學課程](./create-host-pools-azure-marketplace.md)
