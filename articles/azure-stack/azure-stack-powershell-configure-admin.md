---
title: 以操作員的身分使用 PowerShell 連線到 Azure Stack | Microsoft Docs
description: 了解如何以操作員的身分使用 PowerShell 連線到 Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: d5b2b1a73a15874072caacd7549c1c328e7edb26
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116553"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>以操作員的身分使用 PowerShell 連線到 Azure Stack

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

您可以將 Azure Stack 設定成使用 PowerShell 來管理資源，例如建立供應項目、方案、配額及警示。 本主題將協助您設定操作員環境。

## <a name="prerequisites"></a>必要條件

從[開發套件](./asdk/asdk-connect.md#connect-with-rdp)，或從 Windows 型外部用戶端 (如果您[透過 VPN 連線至 ASDK](./asdk/asdk-connect.md#connect-with-vpn))，執行下列先決條件。 

 - 安裝 [Azure Stack 相容的 Azure PowerShell 模組](azure-stack-powershell-install.md)。  
 - 下載[處理 Azure Stack 所需的工具](azure-stack-powershell-download.md)。  

## <a name="connect-with-azure-ad"></a>與 Azure AD 連線

使用 PowerShell 來設定 Azure Stack 操作員環境。 執行下列其中一個指令碼：請將 Azure Active Directory (Azure AD) tenantName 和 Azure Resource Manager 端點值取代為您自己的環境設定。 <!-- GraphAudience endpoint -->

```PowerShell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>與 AD FS 連線

使用 PowerShell 與 Azure Active Directory 同盟服務 (Azure AD FS) 連線至 Azure Stack 操作員環境。 針對 Azure Stack 開發套件，此 Azure Resource Manager 值會設定為 `https://adminmanagement.local.azurestack.external`。 若要取得 Azure Stack 整合式系統的 Azure Resource Manager 端點，請與您的服務提供者連絡。

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS 僅支援利用使用者身分識別的互動式驗證。 如果需要認證物件，您必須使用服務主體 (SPN)。 如需使用 Azure Stack 和 AD FS 將服務主體設定為身分識別管理服務的詳細資訊，請參閱[管理 AD FS 的服務主體](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs)。

## <a name="test-the-connectivity"></a>測試連線

一切都已準備就緒，接下來請使用 PowerShell 在 Azure Stack 中建立資源。 例如，您可以建立應用程式的資源群組，並新增虛擬機器。 若要建立名為 **MyResourceGroup** 的資源群組，請使用下列命令。

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>後續步驟

 - [開發適用於 Azure Stack 的範本](user/azure-stack-develop-templates.md)
 - [使用 PowerShell 部署範本](user/azure-stack-deploy-template-powershell.md)
