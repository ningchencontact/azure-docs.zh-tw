---
title: "如何使用 Azure PowerShell 建立 SQL Server Vm |Microsoft 文件"
description: "提供步驟和 PowerShell 命令建立 Azure VM 的 SQL Server 虛擬機器圖庫映像。"
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/29/2017
ms.author: jroth
ms.openlocfilehash: 5babea628180501e959387f80dac55618051f552
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-sql-server-virtual-machines-with-azure-powershell"></a>如何使用 Azure PowerShell 建立 SQL Server 虛擬機器

本指南說明將選項設定為使用 Azure PowerShell 建立 Windows SQL Server Vm。 簡化 Azure PowerShell 範例中使用多個預設值，請參閱[SQL VM Azure PowerShell 快速入門](quickstart-sql-vm-create-powershell.md)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

本快速入門需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

## <a name="configure-your-subscription"></a>設定您的訂用帳戶

1. 開啟 PowerShell 並執行建立您的 Azure 帳戶存取**新增 AzureRmAccount**命令。

   ```PowerShell
   Add-AzureRmAccount
   ```

1. 您應該會看到登入畫面，輸入您的認證。 請使用與登入 Azure 入口網站相同的電子郵件和密碼。

## <a name="define-image-variables"></a>定義映像變數
若要簡化重複使用及指令碼建立，請啟動藉由定義變數的數目。 視需要變更參數值，但在修改所提供的值時，請注意與名稱長度和特殊字元相關的命名限制。

### <a name="location-and-resource-group"></a>位置和資源群組
您可以使用兩個變數來定義資料區域與資源群組到其中，您建立虛擬機器的其他資源。

視需要修改並執行下列 Cmdlet 來初始化這些變數。

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>儲存體屬性
使用下列變數來定義儲存體帳戶和虛擬機器所要使用的儲存體類型。

視需要修改並執行下列 Cmdlet 來初始化這些變數。 請注意，在此範例中，我們使用 [進階儲存體](../premium-storage.md)，這是針對生產環境工作負載建議使用的儲存體。

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>網路屬性
使用下列變數來定義網路介面、TCP/IP 配置方法、虛擬網路名稱、虛擬子網路名稱、虛擬網路的 IP 位址範圍、子網路的 IP 位址範圍，以及虛擬機器中的網路所要使用的公用網域名稱標籤。

視需要修改並執行下列 Cmdlet 來初始化這些變數。

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>虛擬機器屬性
使用下列變數來定義虛擬機器名稱、電腦名稱、虛擬機器大小和虛擬機器的作業系統磁碟名稱。

視需要修改並執行下列 Cmdlet 來初始化這些變數。

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>選擇 SQL Server 映像
您可以使用下列變數來定義要用於虛擬機器的 SQL Server 映像。

1. 首先，列出所有的 SQL Server 映像供應項目**Get AzureRmVMImageOffer**命令：

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. 此教學課程中，使用下列變數來指定 SQL Server 2017 上 Windows Server 2016。

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. 下一步 時提供的可用版本清單。

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. 本教學課程中，使用 SQL Server 2017 Developer edition (**SQLDEV**)。 Developer 版本免費授權用於測試及開發，而且只需要支付執行 VM 的費用。

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>建立資源群組
在 Resource Manager 部署模型中，您所建立的第一個物件是資源群組。 使用[新增 AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)指令程式來建立 Azure 資源群組和其資源的資源群組名稱和您先前初始化之變數所定義的位置。

執行下列 Cmdlet 來建立新的資源群組。

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶
虛擬機器需要作業系統磁碟和 SQL Server 資料和記錄檔的儲存體資源。 為了簡單起見，我們會同時建立單一磁碟。 您可以在之後使用 [Add-Azure Disk](/powershell/module/azure/add-azuredisk) Cmdlet 來連結額外的磁碟，以便將您的 SQL Server 資料和記錄檔放在專用的磁碟上。 使用[新增 AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet 來建立新的資源群組，並使用儲存體帳戶名稱、 儲存體的 Sku 名稱和位置使用先前初始化之變數定義的標準儲存體帳戶.

執行下列 Cmdlet 來建立新的儲存體帳戶。

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> 建立儲存體帳戶可能需要幾分鐘的時間。

## <a name="create-network-resources"></a>建立網路資源
虛擬機器需要數個網路資源才能連接網路。

* 每部虛擬機器都需要虛擬網路。
* 虛擬網路必須定義至少一個子網路。
* 網路介面必須以公用或私人 IP 位址定義。

### <a name="create-a-virtual-network-subnet-configuration"></a>建立虛擬網路子網路組態
開始建立虛擬網路的子網路組態。 教學課程中，我們會建立預設的子網路使用[新增 AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) cmdlet。 我們會建立虛擬網路子網路組態設定與使用您先前初始化之變數定義的子網路名稱和位址首碼。

> [!NOTE]
> 您可以使用這個 Cmdlet 來定義虛擬網路子網路組態的其他屬性，但這已超出本教學課程的範圍。

執行下列 Cmdlet 來建立虛擬子網路組態。

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>建立虛擬網路
接下來，建立您的虛擬網路使用[新增 AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) cmdlet。 建立新的資源群組中的虛擬網路，以名稱、 位置和位址前置詞會定義使用先前初始化之變數及使用您在上一個步驟中定義的子網路組態。

執行下列 Cmdlet 來建立虛擬網路。

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>建立公用 IP 位址
我們現已定義虛擬網路，我們還必須設定 IP 位址才能連接至虛擬機器。 此教學課程中，我們會建立使用動態 IP 位址以支援網際網路連接的公用 IP 位址。 使用[新增 AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) cmdlet 來建立公用 IP 位址在先前建立的資源群組中，並以名稱、 位置、 配置方法，以及使用變數定義的 DNS 網域名稱標籤，您先前初始化。

> [!NOTE]
> 您可以使用這個 Cmdlet 來定義公用 IP 位址的其他屬性，但這已超出本初期教學課程的範圍。 您也可以建立私人位址或具有靜態位址的位址，但這也已超出本教學課程的範圍。

執行下列 Cmdlet 來公用 IP 位址。

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>建立網路安全性群組
若要保護的 VM 和 SQL Server 的流量，建立網路安全性群組。

1. 首先，建立網路安全性群組規則以允許遠端桌面連線的 rdp。

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. 設定網路安全性群組規則，允許 TCP 通訊埠 1433年上的流量。 這在網際網路上可連接至 SQL Server。

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. 然後建立網路安全性群組。

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>建立網路介面
我們現在已準備好建立虛擬機器將要使用的網路介面。 我們稱之為[新增 AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)先前定義的指令程式可建立我們的網路介面，稍早建立的資源群組，並使用名稱、 位置、 子網路和公用 IP 位址。

執行下列 Cmdlet 來建立網路介面。

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>設定 VM 物件
我們現已定義儲存體和網路資源，我們便準備好定義虛擬機器的計算資源。 教學課程中，我們將指定的虛擬機器大小和各種作業系統屬性，指定我們先前建立的網路介面定義 blob 儲存體，，，然後指定作業系統磁碟。

### <a name="create-the-vm-object"></a>建立 VM 物件
開始指定的虛擬機器大小。 在本教學課程諸ㄥ，我們會指定 DS13。 我們稱之為[新增 AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)指令程式來建立可設定的虛擬機器物件的名稱和定義使用您先前初始化之變數的大小。

執行下列 Cmdlet 來建立虛擬機器物件。

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>建立認證物件，以保留本機系統管理員認證的名稱和密碼
我們必須先提供本機系統管理員帳戶的認證做為安全字串，才可以設定虛擬機器的作業系統屬性。 若要完成此動作，使用[Get-credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet。

執行下列 cmdlet，並在 [PowerShell 認證要求] 視窗中，輸入要用於虛擬機器中的本機系統管理員帳戶密碼與名稱。

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>設定虛擬機器的作業系統屬性
我們已經準備好設定使用的虛擬機器的作業系統內容[組 AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) cmdlet 將作業系統的類型設定為 Windows，需要[虛擬機器代理程式](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)若要安裝，指定此 cmdlet 會啟用自動更新並設定虛擬機器名稱、 電腦名稱，以及使用先前初始化之變數的認證。

執行下列 Cmdlet 來設定虛擬機器的作業系統屬性。

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>將網路介面新增至虛擬機器
接下來，我們加入我們建立的網路介面先前虛擬機器。 呼叫[新增 AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) cmdlet 來新增網路介面使用您先前定義的網路介面變數。

執行下列 Cmdlet 來設定虛擬機器的網路介面。

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>設定虛擬機器所要使用之磁碟的 Blob 儲存體位置
接下來，將 blob 儲存體位置，使用您先前定義的變數之虛擬機器所使用的磁碟。

執行下列 Cmdlet 來設定 Blob 儲存體位置。

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>設定虛擬機器的作業系統磁碟屬性
接下來，設定作業系統磁碟的虛擬機器的內容。 使用[組 AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk)指令程式來指定將來自虛擬機器的作業系統映像，來設定快取讀取，僅 （因為正在安裝 SQL 伺服器相同的磁碟上），並定義虛擬機器名稱和作業系統磁碟使用我們先前定義的變數定義。

執行下列 Cmdlet 來設定虛擬機器的作業系統磁碟屬性。

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>指定虛擬機器的平台映像
最後一個設定步驟是指定虛擬機器的平台映像。 在本教學課程中，我們會使用最新的 SQL Server 2016 CTP 映像。 使用[組 AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage)指令程式可使用此映像，您先前定義的變數所定義。

執行下列 Cmdlet 來設定虛擬機器的平台映像。

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>建立 SQL VM
您現在已完成設定步驟，即可開始建立虛擬機器。 使用[新增 AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 來建立虛擬機器使用，我們已定義的變數。

執行下列 Cmdlet 來建立虛擬機器。

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

虛擬機器已建立。

> [!NOTE]
> 您可以忽略 bot 診斷的相關錯誤。 標準儲存體帳戶會建立開機診斷，因為指定的儲存體帳戶的虛擬機器的磁碟是進階儲存體帳戶。

## <a name="install-the-sql-iaas-agent"></a>安裝 SQL Iaas 代理程式
SQL Server 虛擬機器支援自動化的管理功能與[SQL Server IaaS 代理程式延伸模組](virtual-machines-windows-sql-server-agent-extension.md)。 新的 VM 上安裝代理程式，請執行下列命令會在建立之後。

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>移除測試 VM

如果您不需要持續執行的 VM，您可以停止它在不使用時，避免不必要的費用。 下列命令會停止 VM，但會保留供未來使用。

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

您也可以永久刪除與虛擬機器相關聯的所有資源**移除 AzureRmResourceGroup**命令。 這也會永久刪除虛擬機器，因此請小心使用此命令。

## <a name="example-script"></a>範例指令碼
下列指令碼包含本教學課程的完整 PowerShell 指令碼。 假設您已經設定 Azure 訂用帳戶與 **Add-AzureRmAccount** 和 **Select-AzureRmSubscription** 命令搭配使用。

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>後續步驟
建立虛擬機器之後，您可以：

- 連接到虛擬機器使用遠端桌面 (RDP)。
- 在入口網站中設定 SQL Server 設定，讓您的 VM，包括：
   - [儲存體設定](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [自動化的管理工作](virtual-machines-windows-sql-server-agent-extension.md)
- [設定連線](virtual-machines-windows-sql-connect.md)。
- 連接用戶端與新的 SQL Server 執行個體的應用程式。

