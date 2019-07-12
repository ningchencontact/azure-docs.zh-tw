---
title: 如何變更 SQL Server VM 在 Azure 中的授權模型
description: 以 '攜帶-您擁有的授權' 使用 Azure Hybrid Benefit，了解如何切換 '預付型方案 」 從 Azure SQL 虛擬機器的授權。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786771"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>如何變更 SQL Server 虛擬機器在 Azure 中的授權模型
本文說明如何在 Azure 中使用新的 SQL VM 資源提供者 **Microsoft.SqlVirtualMachine**，來變更 SQL Server 虛擬機器的授權模型。

有兩種授權模型的虛擬機器 (VM) 主控 SQL Server-隨用隨付及 Azure Hybrid Benefit (適 AHB)。 而現在，使用 Azure 入口網站、 Azure CLI 或 PowerShell 您可以修改您的 SQL Server VM 的授權模式。 

**隨用隨付**(PAYG) 模型意謂著執行 Azure VM 的每秒鐘費用都包含 SQL Server 授權的費用。
[Azure Hybrid Benefit (適 AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/)可讓您使用執行 SQL Server VM 上的 SQL Server 授權。 

Microsoft SQL Server 的 Azure Hybrid Benefit 可讓您使用具有軟體保證 （「 合格授權 」） Azure 虛擬機器上的 SQL Server 授權。 使用適用於 SQL Server 的 Azure Hybrid Benefit，客戶不會收取 VM 上的 SQL Server 授權的使用方式，但它們還是必須支付的基礎雲端計算 （也就是基本費率）、 儲存體，和備份，以及其 u 相關聯的 I/O 成本se （視情況） 的服務。

依據 Microsoft 產品條款 」 客戶必須指出它們使用的 Azure SQL Database （受控執行個體、 彈性集區和單一資料庫），Azure Data Factory、 SQL Server Integration Services 或在 Azure 的混合式的 SQL Server 虛擬機器SQL Server 的 benefit 在 Azure 上設定工作負載時。 」

若要指出 Azure VM 上的 SQL Server 的 Azure Hybrid Benefit 使用並符合規範，有三個選項：

1. 佈建虛擬機器從 Azure marketplace，僅適用於 Enterprise 合約客戶使用 BYOL 的 SQL Server 映像。
1. 佈建虛擬機器從 Azure marketplace 使用 PAYG SQL Server 映像，並啟用適 AHB。
1. 自我手動安裝 Azure VM 上的 SQL Server[註冊其 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)並啟用適 AHB。

VM 佈建，而且可以隨時之後變更時，會設定 SQL server 的授權類型。 授權模式之間切換時，會產生**停機**，不會重新啟動 VM，新增**無須支付額外費用**(事實上，啟用 適 AHB*減少*成本) 以及為**立即生效**。 

## <a name="prerequisites"></a>先決條件

使用 SQL VM 資源提供者需要 SQL IaaS 擴充。 因此，若要繼續運用 SQL VM 資源提供者，您需要下列項目：
- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- [軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- A [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)向[SQL VM 資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)安裝。 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>變更 Vm 的授權已向資源提供者 

# <a name="azure-portaltabazure-portal"></a>[Azure 入口網站](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

您可以修改授權模型，直接從入口網站。 

1. 開啟[Azure 入口網站](https://portal.azure.com)，然後啟動[SQL 虛擬機器資源](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource)為您的 SQL Server VM。 
1. 選取 **設定**下方**設定**。 
1. 選取  **Azure Hybrid Benefit**選項，然後選取核取方塊，確認您擁有軟體保證的 SQL Server 授權。 
1. 選取 **套用**底部**設定**頁面。 

![在入口網站中的適 AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

您可以使用 Azure CLI 來變更您的授權模型。  

下列程式碼片段會切換成 BYOL （或使用 Azure Hybrid Benefit） 的隨用隨付授權模型：

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

下列程式碼片段會切換成隨用隨付的您將為您擁有的授權模型： 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
您可以使用 PowerShell 來變更您的授權模型。

下列程式碼片段會切換成 BYOL （或使用 Azure Hybrid Benefit） 的隨用隨付授權模型：

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

下列程式碼片段會切換成隨用隨付的 BYOL 模式：

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Vm 未向資源提供者的變更授權

如果您佈建 SQL Server VM PAYG Marketplace 映像從 SQL 授權類型將會 PAYG。 如果您佈建 SQL Server VM 使用 BYOL 映像從 Azure Marketplace 授權類型將會 AHUB。 所有的 SQL Server Vm 佈建預設 (PAYG) 或 BYOL Azure Marketplace 映像會自動與 SQL VM 資源提供者，註冊，才能變更[授權類型](#change-license-for-vms-already-registered-with-resource-provider)

您只是能夠自我安裝透過 Azure Hybrid Benefit 的 Azure VM 上的 SQL Server，而且您應該[向 SQL VM 資源提供者註冊這些 Vm](virtual-machines-windows-sql-register-with-resource-provider.md)藉由設定 SQL Server 授權，為表示適 AHB 使用方式，根據適的 AHBMicrosoft 產品條款。

如果 SQL VM 向 SQL VM 資源提供者;，您可以只變更授權類型的 SQL Server VM 為 PAYG 或適的 AHB和所有的 SQL Vm 應該向 SQL VM RP 的授權合規性。

## <a name="remarks"></a>備註

 - Azure 雲端方案 (CSP) 客戶可以利用 Azure Hybrid Benefit，第一次部署的隨用隨付的 VM，並再將它轉換成自備-您擁有的授權有作用中 SA。
 - 如果您卸除 SQL Server VM 資源，您將會移回至映像的硬式編碼的授權設定。 
  - 若要變更授權模型的能力是 SQL VM 的資源提供者的功能。 部署在 Azure 入口網站的 marketplace 映像會自動向資源提供者的 SQL Server VM。 不過，自我安裝 SQL Server 的客戶將需要手動[註冊其 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。 
- 將 SQL Server VM 加入至可用性設定組時，需要重新建立 VM。 這類，任何虛擬機器新增到可用性集將會回到預設的隨用隨付授權類型，並適 AHB 必須再次啟用。 


## <a name="limitations"></a>限制

 - 變更授權模型只會提供給具備軟體保證的客戶。
 - SQL Server 的 standard 和 enterprise edition 才支援變更授權模型。 Express、 Web 測試和開發人員的授權變更，不支援。 
 - 使用 Resource Manager 模型部署的虛擬機器只支援變更授權模型。 不支援使用傳統模型所部署的 Vm。 
 - 僅適用於公用雲端安裝變更的授權模型。
 - 支援只在具有單一 NIC （網路介面） 的虛擬機器上的變更授權模型。 在有多個 NIC 的虛擬機器，您應該先移除其中一個 Nic （藉由使用 Azure 入口網站） 之前嘗試程序。 否則，您將會類似下列的錯誤：`The virtual machine '\<vmname\>' has more than one NIC associated.` 雖然您可以將 NIC 新增回 VM 之後變更的授權模式,，透過在 Azure 入口網站中，例如自動修補和備份 [SQL 設定] 頁面完成的作業將不再支援。


## <a name="known-errors"></a>已知錯誤

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>資源 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<資源群組 >' 位於資源群組'\<資源群組 >' 找不到。 此物件上找不到屬性 'sqlServerLicenseType'。 請確認屬性存在，且可以設定。
當您嘗試使用 SQL VM 的資源提供者尚未註冊的 SQL Server VM 上的授權模式變更時，就會發生此錯誤。 您必須註冊資源提供者，以您[訂用帳戶](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)，再將您的 SQL Server VM 向 SQL[資源提供者](virtual-machines-windows-sql-register-with-resource-provider.md)。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>無法驗證參數 'Sku' 上的引數
當您嘗試使用 Azure PowerShell 時，變更您的 SQL Server VM 的授權模型時，可能會遇到這個錯誤 > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

若要解決此錯誤，在切換授權模型時，請將先前所提到之 PowerShell 程式碼片段中的這些行取消註解：

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
使用下列程式碼來確認您的 Azure PowerShell 版本：
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


