---
title: 設定 Azure Stack 操作員的 PowerShell 環境 | Microsoft Docs
description: 了解如何設定 Azure Stack 操作員的 PowerShell 環境。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: a8ab52de6c57e84bb2c90ce6bcf53ef1b92e30af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>設定 Azure Stack 操作員的 PowerShell 環境

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您可以將 Azure Stack 設定成使用 PowerShell 來管理資源，例如建立供應項目、方案、配額及警示。 本主題將協助您設定操作員環境。 如果您想要設定適用於使用者環境的 PowerShell，請參閱[設定 Azure Stack 使用者的 PowerShell 環境](user/azure-stack-powershell-configure-user.md)一文。

## <a name="prerequisites"></a>先決條件

從[開發套件](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或從 Windows 型外部用戶端 (如果您是[透過 VPN 連線](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn))，執行下列先決條件： 

* 安裝 [Azure Stack 相容的 Azure PowerShell 模組](azure-stack-powershell-install.md)。  
* 下載[處理 Azure Stack 所需的工具](azure-stack-powershell-download.md)。  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>設定操作員環境並登入 Azure Stack

使用 PowerShell 來設定 Azure Stack 操作員環境。 請根據部署類型 (Azure AD 或 AD FS)，執行下列其中一個指令碼：請使用您自己的環境設定來取代 Azure AD tenantName、GraphAudience 端點及 ArmEndpoint 值。

### <a name="azure-active-directory-azure-ad-based-deployments"></a>以 Azure Active Directory (Azure AD) 為基礎的部署

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>以 Active Directory Federation Services (AD FS) 為基礎的部署

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>測試連線

既然您已將一切設定妥當，現在即可使用 PowerShell 在 Azure Stack 內建立資源。 例如，您可以建立應用程式的資源群組，並新增虛擬機器。 若要建立名為 "MyResourceGroup" 的資源群組，請使用下列命令：

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>後續步驟
* [開發適用於 Azure Stack 的範本](user/azure-stack-develop-templates.md)
* [使用 PowerShell 部署範本](user/azure-stack-deploy-template-powershell.md)
