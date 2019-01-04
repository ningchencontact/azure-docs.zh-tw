---
title: 如何使用 PowerShell 檢視受控識別的服務主體
description: 逐步指導您如何使用 PowerShell 檢視受控識別的服務主體。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0ad3a52b837a5f79c9976c4c509e0a8516de1e7d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714101"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>使用 PowerShell 檢視受控識別的服務主體

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您會了解如何使用 PowerShell 來檢視受控識別的服務主體。

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。
- 如果您還沒有 Azure 帳戶，請[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 啟用[虛擬機器](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)或[應用程式](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity)上的系統指派身分識別。
- 如果您選擇在本機安裝和使用 PowerShell，則在執行本教學課程時，您必須使用 Azure PowerShell 模組 5.7.0 版或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 
- 如果您在本機執行 PowerShell，您也需要： 
    - 執行 `Login-AzureRmAccount` 來建立與 Azure 的連線。
    - 安裝[最新版的 PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)。
    - 執行 `Install-Module -Name PowerShellGet -AllowPrerelease` 以取得 `PowerShellGet` 模組的搶鮮版 (執行此命令以安裝 `AzureRM.ManagedServiceIdentity` 模組後，您可能需要以 `Exit` 退出目前的 PowerShell 工作階段)。
    - 執行 `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` 安裝 `AzureRM.ManagedServiceIdentity` 模組的搶鮮版，以執行本文中由使用者指派的受控識別作業。

## <a name="view-the-service-principal"></a>檢視服務主體

下列命令示範如何檢視已啟用系統指派身分識別的 VM 或應用程式所具有的服務主體。 以您自己的值取代 `<VM or application name>`。

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>後續步驟

如需如何使用 PowerShell 檢視 Azure AD 服務主體的詳細資訊，請參閱 [Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal)。


