---
title: 使用 Azure PowerShell 建立 SQL Server Windows VM | Microsoft Docs
description: 本教學課程會示範如何使用 Azure PowerShell 建立 Windows SQL Server 2017 虛擬機器。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aa4ea4e724ec383fc9f22bd56572d2fd0e844abc
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332433"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 來建立 SQL Server Windows 虛擬機器

本快速入門會逐步說明如何使用 Azure PowerShell 來建立 SQL Server 虛擬機器。

> [!TIP]
> 本快速入門提供快速佈建及連線到 SQL VM 的途徑。 如需其他用於建立 SQL VM 之 Azure PowerShell 選項的詳細資訊，請參閱[使用 Azure PowerShell 佈建 SQL Server VM 的指南](virtual-machines-windows-ps-sql-create.md)。

> [!TIP]
> 如果您有 SQL Server 虛擬機器的相關問題，請參閱[常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)。

## <a id="subscription"></a> 取得 Azure 訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


## <a id="powershell"></a> 取得 Azure PowerShell

本快速入門需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

## <a name="configure-powershell"></a>設定 PowerShell

1. 開啟 PowerShell 並執行 **Connect-AzureRmAccount** 來建立您的 Azure 帳戶存取權限。

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. 您應該會看到輸入認證的畫面。 請使用與登入 Azure 入口網站相同的電子郵件和密碼。

## <a name="create-a-resource-group"></a>建立資源群組

1. 使用唯一的資源群組名稱來定義變數。 為了簡化快速入門的其餘部分，其餘命令將使用此名稱作為其他資源名稱的基礎。

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. 定義所有 VM 資源的目標 Azure 區域之位置。

   ```PowerShell
   $Location = "East US"
   ```

1. 建立資源群組。

   ```PowerShell
   New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>設定網路設定

1. 建立虛擬網路、子網路和公用 IP 位址。 這些資源用來提供虛擬機器的網路連線能力，並可將它連線到網際網路。

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. 建立網路安全性群組。 設定規則以允許遠端桌面 (RDP) 和 SQL Server 連線。

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. 建立網路介面。

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>建立 SQL VM

1. 定義您用來登入 VM 的認證。 使用者名稱為 "azureadmin"。 在執行此命令之前，請務必先變更 \<password>。

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. 建立虛擬機器設定物件，然後建立 VM。 下列命令會在 Windows Server 2016 上建立 SQL Server 2017 Developer Edition VM。

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzureRmVMConfig -VMName $VMName -VMSize Standard_DS13_V2 | `
      Set-AzureRmVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzureRmVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzureRmVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > 建立 VM 需要幾分鐘的時間。

## <a name="install-the-sql-iaas-agent"></a>安裝 SQL IaaS 代理程式

若要取得入口網站整合與 SQL VM 的功能，您必須先安裝 [SQL Server IaaS 代理程式延伸模組](virtual-machines-windows-sql-server-agent-extension.md)。 若要在新的 VM 上安裝代理程式，請在 VM 建立後執行下列命令。

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>從遠端桌面連接到 VM

1. 使用下列命令擷取新 VM 的公用 IP 位址。

   ```PowerShell
   Get-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. 將傳回的 IP 位址以命令列參數的形式傳至 **mstsc**，以啟動新 VM 的遠端桌面工作階段。

   ```
   mstsc /v:<publicIpAddress>
   ```

1. 當提示輸入認證，請選擇不同帳戶的輸入認證。 輸入包含前置反斜線的使用者名稱 (例如 `\azureadmin`)，以及您先前在本快速入門中設定的密碼。

## <a name="connect-to-sql-server"></a>連接到 SQL Server

1. 登入遠端桌面工作階段之後，從 [開始] 功能表啟動 **SQL Server Management Studio 2017**。

1. 在 [連線到伺服器] 對話方塊中，保留預設值。 伺服器名稱為 VM 的名稱。 驗證已設為 [Windows 驗證]。 選取 [ **連接**]。

您現在已從本機連線到 SQL Server。 若要從遠端連線，您必須從入口網站或以手動方式[設定連線](virtual-machines-windows-sql-connect.md)。

## <a name="clean-up-resources"></a>清除資源

如果您不需要持續執行 VM，您可以在不使用時將其停止，以避免不必要的費用。 下列命令會停止 VM，但會將其保留供未來使用。

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

您也可以使用 **Remove-AzureRmResourceGroup**命令，將與虛擬機器相關聯的所有資源永久刪除。 這麼做也會永久刪除虛擬機器，因此請小心使用此命令。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure PowerShell 建立了 SQL Server 2017 虛擬機器。 若要深入了解如何將您的資料移轉至新的 SQL Server，請參閱下列文章。

> [!div class="nextstepaction"]
> [將資料庫移轉至 SQL VM](virtual-machines-windows-migrate-sql.md)
