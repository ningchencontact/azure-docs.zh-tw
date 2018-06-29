---
title: 設定 Azure Stack 使用者的 PowerShell 環境 | Microsoft Docs
description: 設定 Azure Stack 使用者的 PowerShell 環境
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/15/2018
ms.author: mabrigg
ms.reviewer: Balsu.G
ms.openlocfilehash: bcd1c53221028a852550fa429abcb9f8e9523ed4
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752416"
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>設定 Azure Stack 使用者的 PowerShell 環境

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

使用本文中的指示，為 Azure Stack 使用者設定 PowerShell 環境。
設定環境之後，您可以使用 PowerShell 來管理 Azure Stack 資源。 例如，您可以使用 PowerShell 來訂閱供應項目、建立虛擬機器及部署 Azure Resource Manager 範本。

>[!NOTE]
>本文的適用範圍為 Azure Stack 使用者環境。 如果想要針對雲端操作員環境設定 PowerShell，請參閱[設定 Azure Stack 操作員的 PowerShell 環境](../azure-stack-powershell-configure-admin.md)一文。

## <a name="prerequisites"></a>先決條件

從[開發套件](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或從 Windows 型外部用戶端 (如果您[透過 VPN 連線](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn))，設定下列先決條件：

* 安裝 [Azure Stack 相容的 Azure PowerShell 模組](azure-stack-powershell-install.md)。
* 下載[處理 Azure Stack 所需的工具](azure-stack-powershell-download.md)。

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>設定使用者環境並登入 Azure Stack

根據 Azure Stack 部署類型 (Azure AD 或 AD FS)，執行下列其中一個指令碼來設定 Azure Stack 的 PowerShell。

務必使用 Azure Stack 組態中的值取代下列指令碼變數：

* AAD 租用戶名稱
* ArmEndpoint

### <a name="azure-active-directory-aad-based-deployments"></a>以 Azure Active Directory (AAD) 為基礎的驗證

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>以 Active Directory Federation Services (AD FS) 為基礎的部署

  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID
  ```

## <a name="register-resource-providers"></a>註冊資源提供者

沒有透過入口網站部署任何資源的新使用者訂用帳戶，並不會自動註冊資源提供者。 執行下列指令碼，即可明確註冊資源提供者：

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>測試連線

當您將一切設定妥當時，使用 PowerShell 在 Azure Stack 中建立資源，以測試連線能力。 在測試時，建立應用程式的資源群組並新增虛擬機器。 若要建立名為 "MyResourceGroup" 的資源群組，請執行下列命令：

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>後續步驟

* [開發適用於 Azure Stack 的範本](azure-stack-develop-templates.md)
* [使用 PowerShell 部署範本](azure-stack-deploy-template-powershell.md)
