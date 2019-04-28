---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 02/01/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 820a6a4da9f5c466e694f247d09393474d8464ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407550"
---
您可以建立 VM 的遠端桌面連線，以連線至已部署至 VNet 的 VM。 一開始確認您可以連線至 VM 的最佳方法是使用其私人 IP 位址 (而不是電腦名稱) 進行連線。 这种方式是测试能否进行连接，而不是测试名称解析是否已正确配置。

1. 找出私人 IP 位址。 您可以使用多種方法找到 VM 的私人 IP 位址。 以下，我們示範 Azure 入口網站和 PowerShell 適用的步驟。

   - Azure 门户 - 在 Azure 门户中定位虚拟机。 檢視 VM 的屬性。 系統會列出私人 IP 位址。

   - PowerShell - 使用範例來檢視資源群組中的 VM 和私人 IP 位址清單。 在使用此示例之前不需对其进行修改。

     ```azurepowershell
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. 验证是否已使用 VPN 连接连接到 VNet。
3. 在工作列上的搜尋方塊中輸入「RDP」或「遠端桌面連線」以開啟遠端桌面連線，然後選取 [遠端桌面連線]。 您也可以使用 PowerShell 中的 'mstsc' 命令開啟遠端桌面連線。 
4. 在 [遠端桌面連線] 中，輸入 VM 的私人 IP 位址。 您可以按一下 [顯示選項] 來調整其他設定，然後進行連線。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>針對 VM 的 RDP 連線進行疑難排解

如果您無法透過 VPN 連線與虛擬機器連線，請檢查下列各項：

- 验证 VPN 连接是否成功。
- 確認您是連線至 VM 的私人 IP 位址。
- 如果您可以使用私人 IP 位址 (而非電腦名稱) 來連線至 VM，請確認您已正確設定 DNS。 如需 VM 的名稱解析運作方式的詳細資訊，請參閱 [VM 的名稱解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。
- 若要详细了解 RDP 连接，请参阅[排查到 VM 的远程桌面连接问题](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。
