---
title: 如何在 Azure 中變更 SQL Server VM 的授權模型 | Microsoft Docs
description: 了解如何在 Azure 中切換 SQL 虛擬機器的授權。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
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
ms.openlocfilehash: 1fb67600ea01629e7bf3ab4c7c470e4727b0e923
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393180"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>如何在 Azure 中變更 SQL Server 虛擬機器的授權模型
本文說明如何在 Azure 中使用新的 SQL VM 資源提供者 **Microsoft.SqlVirtualMachine**，來變更 SQL Server 虛擬機器的授權模型。 有兩個授權裝載 SQL Server-隨用隨付，虛擬機器 (VM) 的模型和自備授權 (BYOL)。 而現在，使用 Azure 入口網站、 Azure CLI 或 PowerShell 您可以修改您的 SQL Server VM 會使用哪個授權模型。 

**隨用隨付**(PAYG) 模型意謂著執行 Azure VM 的每秒鐘費用都包含 SQL Server 授權的費用。

**攜帶-您擁有的授權**(BYOL) 模式，也就是[Azure Hybrid Benefit (適 AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/)，並可讓您使用執行 SQL Server VM 上的 SQL Server 授權。 如需價格的詳細資訊，請參閱 [SQL Server VM 定價指南](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)。

在兩個授權模型之間切換時，**不會產生停機時間**、不會重新啟動 VM、**無須支付額外費用** (事實上，啟動 AHB 會「降低」  成本)，而且**立即生效**。 

## <a name="remarks"></a>備註


 - Azure 雲端方案 (CSP) 客戶可以利用 Azure Hybrid Benefit，第一次部署的隨用隨付的 VM，並再將它轉換成自備-您擁有的授權。 
 - 向資源提供者註冊自訂的 SQL Server VM 映像，當指定授權類型 = 'AHUB'。 離開授權類型為空白，或指定 'PAYG' 會造成失敗的註冊。 
 - 如果您卸除 SQL Server VM 資源，您將會移回至映像的硬式編碼的授權設定。 
 - 將 SQL Server VM 加入至可用性設定組時，需要重新建立 VM。 這類，任何虛擬機器加入至可用性集合將會回到預設的隨用隨付授權類型，並適 AHB 必須再次啟用。 
 - 若要變更授權模型的能力是 SQL VM 的資源提供者的功能。 部署在 Azure 入口網站的 marketplace 映像會自動向資源提供者的 SQL Server VM。 不過，自我安裝 SQL Server 的客戶將需要手動[註冊其 SQL Server VM](#register-sql-server-vm-with-the-sql-vm-resource-provider)。 
 

 
## <a name="limitations"></a>限制

 - 轉換授權模型的功能目前僅適用於以隨用隨付 SQL Server VM 映像啟動的狀況。 如果您從入口網站使用自備授權映像來啟動，則無法將該映像轉換成隨用隨付。
 - 使用 Resource Manager 模型部署的虛擬機器只支援變更授權模型。 不支援使用傳統模型所部署的 Vm。 
 - 僅適用於公用雲端安裝變更的授權模型。
 - 支援只在具有單一 NIC （網路介面） 的虛擬機器上的變更授權模型。 在有多個 NIC 的虛擬機器，您應該先移除其中一個 Nic （藉由使用 Azure 入口網站） 之前嘗試程序。 否則，您將會類似下列的錯誤：`The virtual machine '\<vmname\>' has more than one NIC associated.` 雖然您可以將 NIC 新增回 VM 之後變更的授權模式,，透過 SQL 組態刀鋒視窗中，例如自動修補和備份，完成的作業將不再支援。

## <a name="prerequisites"></a>必要條件

使用 SQL VM 資源提供者需要 SQL IaaS 擴充。 因此，若要繼續運用 SQL VM 資源提供者，您需要下列項目：
- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- [軟體保證](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)。 
- A*隨用隨付* [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)具有[SQL IaaS 延伸模組](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)安裝。 

## <a name="with-the-azure-portal"></a>透過 Azure 入口網站

您可以修改授權模型，直接從入口網站。 

1. 瀏覽至您的 SQL Server VM 內[Azure 入口網站](https://portal.azure.com)。 
1. 選取  **SQL Server 組態**中**設定**窗格。 
1. 選取 **編輯**中**SQL Server 授權**窗格，即可修改授權。 

![在入口網站中的適 AHB](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > 此選項不適用於將為您擁有的授權映像。 

## <a name="with-azure-cli"></a>透過 Azure CLI

您可以使用 Azure CLI 來變更您的授權模型。  

下列程式碼片段會切換成 BYOL （或使用 Azure Hybrid Benefit） 的隨用隨付授權模型：

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

下列程式碼片段會切換成隨用隨付的您將為您擁有的授權模型： 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```
## <a name="with-powershell"></a>透過 PowerShell
您可以使用 PowerShell 來變更您的授權模型。

下列程式碼片段會切換成 BYOL （或使用 Azure Hybrid Benefit） 的隨用隨付授權模型：

```powershell
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

```powershell
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


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>使用 SQL VM 的資源提供者註冊 SQL Server VM
在某些情況下，您可能需要以手動方式與 SQL VM 的資源提供者註冊您的 SQL Server VM。 若要這樣做，您可能也需要以手動方式註冊您的訂用帳戶資源提供者。 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>SQL VM 資源提供者註冊訂用帳戶 

若要向 SQL 資源提供者註冊您的 SQL Server VM，您必須向訂用帳戶註冊資源提供者。 您可以使用 Azure 入口網站或 Azure CLI 來這麼做。 

#### <a name="with-the-azure-portal"></a>透過 Azure 入口網站
下列步驟將註冊 SQL 資源提供者，您使用 Azure 入口網站的 Azure 訂用帳戶。 

1. 開啟 Azure 入口網站並瀏覽至 [所有服務]  。 
1. 瀏覽至 [訂用帳戶]  然後選取感興趣的訂用帳戶。  
1. 在 [訂用帳戶]  刀鋒視窗中，瀏覽至 [資源提供者]  。 
1. 在篩選條件中輸入 `sql` 以顯示 SQL 相關的資源提供者。 
1. 取決於您所需的動作，針對 **Microsoft.SqlVirtualMachine** 提供者選取 [註冊]  、[重新註冊]  或 [取消註冊]  。 

   ![修改提供者](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>透過 Azure CLI
下列程式碼片段會註冊您的 Azure 訂用帳戶的 SQL VM 資源提供者。 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>透過 PowerShell

下列程式碼片段會註冊您的 Azure 訂用帳戶的 SQL 資源提供者。

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>向 SQL 資源提供者註冊 SQL Server VM
SQL VM 的資源提供者註冊訂用帳戶之後, 您就可以與使用 Azure CLI 資源提供者註冊您的 SQL Server VM。 

#### <a name="with-azure-cli"></a>透過 Azure CLI
註冊 SQL Server VM 使用 Azure CLI 搭配下列程式碼片段： 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```
#### <a name="with-powershell"></a>透過 PowerShell
註冊 SQL Server VM 搭配使用 PowerShell 與下列程式碼片段：

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```



## <a name="known-errors"></a>已知錯誤

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>虛擬機器上未安裝 SQL IaaS 擴充
SQL IaaS 擴充是向 SQL VM 資源提供者註冊 SQL Server VM 的必要先決條件。 如果您在安裝 SQL IaaS 擴充之前嘗試註冊 SQL Server VM，將會遇到下列錯誤：

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

若要解決此問題，請在嘗試註冊 SQL Server VM 之前安裝 SQL IaaS 擴充。 

  > [!NOTE]
  > 安裝 SQL IaaS 擴充將會重新啟動 SQL Server 服務，因此僅應在維護期間進行。 如需詳細資訊，請參閱 [SQL IaaS 擴充安裝](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation)。 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>資源 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<資源群組 >' 位於資源群組'\<資源群組 >' 找不到。 此物件上找不到屬性 'sqlServerLicenseType'。 請確認屬性存在，且可以設定。
當您嘗試使用 SQL 資源提供者尚未註冊的 SQL Server VM 上的授權模式變更時，就會發生此錯誤。 您必須註冊資源提供者，以您[訂用帳戶](#register-sql-vm-resource-provider-with-subscription)，再將您的 SQL Server VM 向 SQL[資源提供者](#register-sql-server-vm-with-sql-resource-provider)。 

### <a name="cannot-validate-argument-on-parameter-sku"></a>無法驗證參數 'Sku' 上的引數
您可能會在使用版本大於 4.0 的 Azure PowerShell 變更 SQL Server VM 的授權模型時遇到此錯誤：設定 AzResource:無法驗證參數 'Sku' 引數。 引數為 null 或空白。 提供的引數不是 null 或空白，並再試一次命令。
若要解決此錯誤，在切換授權模型時，請將先前所提到之 PowerShell 程式碼片段中的這些行取消註解：

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

使用下列程式碼來確認您的 Azure PowerShell 版本：

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章： 

* [Windows VM 上的 SQL Server 概觀](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定價指引](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 版本資訊](virtual-machines-windows-sql-server-iaas-release-notes.md)


