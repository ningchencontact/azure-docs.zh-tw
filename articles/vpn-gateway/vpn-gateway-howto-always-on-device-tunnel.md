---
title: 設定 VPN 閘道的 Always On VPN 通道
description: 設定 VPN 閘道 Always On VPN 通道的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: bc2ec2b952b4f0c6e61fc4953559fa882edfff09
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841148"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>設定 Always On VPN 裝置通道

Windows 10 虛擬私人網路 (VPN) 用戶端的其中一項新功能就是維護 VPN 連線的能力。 Always On 是一項 Windows 10 功能, 可讓使用中的 VPN 設定檔自動連接, 並根據觸發程式保持線上狀態, 也就是使用者登入、網路狀態變更或裝置螢幕作用中。

Azure 虛擬網路閘道可以與 Windows 10 Always On 搭配使用, 以建立持續的使用者通道, 以及 Azure 的裝置通道。 本文將協助您設定 Always ON VPN 裝置通道。

Always On VPN 連接包含兩種類型的通道:

* **裝置**通道會在使用者登入裝置之前, 連線到指定的 VPN 伺服器。 登入前的連線案例和裝置管理用途是使用裝置通道。

* 使用者通道只會在使用者登入裝置之後連線。 使用者通道可讓使用者透過 VPN 伺服器存取組織資源。

裝置通道和使用者通道都是獨立運作的 VPN 設定檔。 它們可以同時連接, 而且可以適當地使用不同的驗證方法和其他 VPN 設定。

## <a name="1-configure-the-gateway"></a>1.設定閘道

將 VPN 閘道設定為使用 IKEv2 和以憑證為基礎的驗證, 並使用這個[點對站文章](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。

## <a name="2-configure-the-device-tunnel"></a>2.設定裝置通道

必須符合下列需求, 才能成功建立裝置通道:

* 裝置必須是執行 Windows 10 企業版或教育版1709或更新版本的已加入網域的電腦。
* 通道只能針對 Windows 內建 VPN 解決方案進行設定, 而且是使用 IKEv2 與電腦憑證驗證來建立。 
* 每一裝置只能設定一個裝置通道。

1. 在 Windows 10 用戶端上安裝用戶端憑證, 如此[點對站 VPN 用戶端一文](point-to-site-how-to-vpn-client-install-azure-cert.md)所示。 憑證必須位於本機電腦存放區中。
1. 使用[這些指示](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)來建立 VPN 設定檔, 並在本機系統帳戶的內容中設定裝置通道。

### <a name="configuration-example-for-device-tunnel"></a>裝置通道的設定範例

設定虛擬網路閘道並在 Windows 10 用戶端的本機電腦存放區中安裝用戶端憑證之後, 請使用下列範例來設定用戶端裝置通道。

1. 複製下列文字, 並將它儲存為***devicecert***。

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. 複製下列文字, 並將它儲存為***VPNProfile***與**devicecert**相同的資料夾。 編輯下列文字以符合您的環境。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. 從[Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec)下載**PsExec** , 並將檔案解壓縮至**C:\PSTools**。
1. 從系統管理員命令提示字元中, 執行以啟動 PowerShell:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. 在 PowerShell 中, 切換至**devicecert**和**VPNProfile**所在的資料夾, 然後執行下列命令:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. 執行**rasphone**。

   ![rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. 尋找**MachineCertTest**專案, 然後按一下 **[連接]** 。

   ![連接](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. 如果連接成功, 請將電腦重新開機。 通道會自動連接。

## <a name="cleanup"></a>清除

若要移除設定檔, 請執行下列命令:

![清除](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>後續步驟

如需疑難排解, 請參閱[Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
