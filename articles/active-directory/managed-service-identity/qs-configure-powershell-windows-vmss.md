---
title: 如何使用 PowerShell 設定 Azure VMSS 上的 MSI
description: 使用 PowerShell 在 Azure VMSS 上設定系統和使用者指派身分識別的逐步指示。
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
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 61fa6c94c0d717fe1e71bf8929f2e3b4a0982562
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903874"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>使用 PowerShell 設定 VMSS 受控服務識別 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受控服務身分識別」會提供自動受控身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 PowerShell，在虛擬機器擴展集 (VMSS) 上執行受控服務識別作業：
- 在 Azure VMSS 上啟用和停用系統指派的身分識別
- 在 Azure VMSS 上新增和移除使用者指派的身分識別

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉受控服務識別，請參閱[概觀](overview.md)一節。 **請務必檢閱[系統指派和使用者指派身分識別之間的差異](overview.md#how-does-it-work)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 如果您尚未安裝[最新版的 Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM)，請先安裝。 

## <a name="system-assigned-managed-identity"></a>系統指派的受控身分識別

在本節中，您會學習如何使用 Azure PowerShell 啟用和移除系統指派的身分識別。

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>在建立 Azure VMSS 時，啟用系統指派的身分識別

若要建立已啟用系統指派身分識別的 VMSS：

1. 請參閱 [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) Cmdlet 參考一文中的「範例 1」，以建立具有系統指派身分識別的 VMSS。  將參數 `-IdentityType SystemAssigned` 新增至 `New-AzureRmVmssConfig` Cmdlet：

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (選擇性) 使用 [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) Cmdlet 上的 `-Name` 和 `-Type` 參數，新增 MSI VMSS 擴充。 您可以傳遞「ManagedIdentityExtensionForWindows」或「ManagedIdentityExtensionForLinux」(取決於 VM 的類型)，並使用 `-Name` 參數為其命名。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

    > [!NOTE]
    > 此步驟是選擇性的，因為您也可以使用 Azure Instance Metadata Service (IMDS) 識別端點以擷取權杖。

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>在現有 Azure VMSS 上啟用系統指派的身分識別

如果您需要在現有 Azure VMSS 上啟用系統指派的身分識別：

1. 使用 `Login-AzureRmAccount` 登入 Azure。 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 此外也請確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」：

   ```powershell
   Login-AzureRmAccount
   ```

2. 先使用 [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss) Cmdlet 擷取 VMSS 屬性。 然後在 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) Cmdlet 上使用 `-IdentityType` 參數啟用系統指派的身分識別：

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. 使用 [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) Cmdlet 上的 `-Name` 和 `-Type` 參數，新增 MSI VMSS 擴充。 您可以傳遞「ManagedIdentityExtensionForWindows」或「ManagedIdentityExtensionForLinux」(取決於 VM 的類型)，並使用 `-Name` 參數為其命名。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>從 Azure VMSS 停用系統指派的身分識別

> [!NOTE]
> 目前不支援從虛擬機器擴展集停用受控服務識別。 同時，您可以在使用系統指派和使用者指派身分識別之間切換。

如果您有不再需要系統指派身分識別，但是仍然需要使用者指派身分識別的虛擬機器擴展集，請使用下列 Cmdlet：

1. 使用 `Login-AzureRmAccount` 登入 Azure。 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 此外也請確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」：

2. 執行下列 Cmdlet：

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>使用者指派的身分識別

在本節中，您會學習如何使用 Azure PowerShell 從 VMSS 新增和移除使用者指派的身分識別。

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>在建立 Azure VMSS 時，指派使用者指派的身分識別

建立新的 VMSS，它具有目前無法透過 PowerShell 支援的使用者指派身分識別。 請參閱下一節，以了解如何將使用者指派的身分識別新增至現有 VMSS。 請回來查看以取得更新資料。

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>將使用者身分識別新增至現有 Azure VMSS

若要將使用者指派的身分識別新增至現有 Azure VMSS：

1. 使用 `Connect-AzureRmAccount` 登入 Azure。 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 此外也請確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」：

   ```powershell
   Connect-AzureRmAccount
   ```

2. 先使用 `Get-AzureRmVM` Cmdlet 擷取 VM 屬性。 然後使用 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) Cmdlet 上的 `-IdentityType` 和 `-IdentityID` 參數，將使用者指派的身分識別新增至 Azure VMSS。 以您自己的值取代 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、`<USER ASSIGNED ID1>`、`USER ASSIGNED ID2`。

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>從 Azure VMSS 移除使用者指派的身分識別

> [!NOTE]
> 目前不支援從虛擬機器擴展集移除所有使用者指派的身分識別，除非您具有系統指派的身分識別。 請回來查看以取得更新資料。

如果您的 VMSS 具有多個使用者指派的身分識別，則您可以使用下列命令移除所有身分識別，但請留下最後一個。 請務必以您自己的值取代 `<RESOURCE GROUP>` 和 `<VMSS NAME>` 參數的值。 `<MSI NAME>` 是使用者指派身分識別的名稱屬性，它應該保留在 VMSS 上。 您可以使用 `az vmss show`，在 VMSS 的身分識別區段中找到此資訊：

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

如果您的 VMSS 同時具有系統指派和使用者指派的身分識別，只要切換為僅使用系統指派的身分識別，即可移除所有使用者指派的身分識別。 使用下列命令：

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>相關內容

- [受控服務識別概觀](overview.md)
- 如需完整的 Azure VM 建立快速入門，請參閱：
  
  - [使用 PowerShell 建立 Windows 虛擬機器](../../virtual-machines/windows/quick-create-powershell.md) 
  - [使用 PowerShell 建立 Linux 虛擬機器](../../virtual-machines/linux/quick-create-powershell.md) 

















