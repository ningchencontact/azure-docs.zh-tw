---
title: 設定適用於 VPN 閘道的一律開啟 」 VPN 通道
description: 若要設定 VPN 閘道的一律開啟 」 VPN 通道的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695754"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>設定 Always On VPN 裝置通道

Windows 10 虛擬私人網路 (VPN) 用戶端的新功能之一是能夠維護 VPN 連線。 永遠開啟 」 是一種 Windows 10 功能，讓作用中的 VPN 設定檔，自動連線並維持連線根據觸發程序 — 也就是使用者登入、 網路狀態變更或作用中裝置螢幕。

Azure 虛擬網路閘道可以用與 Windows 10 Alwayson，來建立持續性使用者通道，以及對 Azure 的裝置通道。 本文將協助您設定 Always ON VPN 裝置通道。

一律開啟 」 VPN 連線中包含兩種類型的通道：

* **裝置通道**使用者登入裝置之前，連接到指定的 VPN 伺服器。 登入前連線案例和裝置管理，請使用 裝置通道。

* **使用者通道**使用者登入裝置後，才會連線。 使用者通道可讓使用者透過 VPN 伺服器存取組織資源。

裝置通道與使用者通道獨立運作，使用自己的 VPN 設定檔。 他們可以連線在此同時，也可以視需要使用不同的驗證方法和其他 VPN 組態設定。

## <a name="1-configure-the-gateway"></a>1.設定閘道

VPN 閘道設定為使用 IKEv2 和憑證型驗證使用此[點對站文章](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。

## <a name="2-configure-the-user-tunnel"></a>2.設定使用者通道

1. 在 Windows 10 用戶端上安裝用戶端憑證，在此所示[點對站 VPN 用戶端文章](point-to-site-how-to-vpn-client-install-azure-cert.md)。 憑證必須位於目前使用者存放區
2. 設定一律開啟 」 VPN 用戶端，透過 PowerShell、 SCCM 或 Intune[這些指示](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)。

## <a name="3-configure-the-device-tunnel"></a>3.設定裝置通道

若要成功建立裝置通道，就必須符合下列需求：

* 裝置必須加入的網域的電腦執行 Windows 10 企業版或教育版本 1709年或更新版本。
* 通道才可設定 Windows 內建的 VPN 解決方案，並且會使用 IKEv2 電腦憑證驗證來建立。 
* 每個裝置，您可以設定只有一個裝置通道。

1. 在 Windows 10 用戶端上安裝用戶端憑證，在此所示[點對站 VPN 用戶端文章](point-to-site-how-to-vpn-client-install-azure-cert.md)。 憑證必須位於本機電腦存放區。
1. 使用[這些指示](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)建立 VPN 設定檔和設定的本機系統帳戶內容中的裝置通道。

### <a name="configuration-example-for-device-tunnel"></a>裝置通道的組態範例

您設定虛擬網路閘道並安裝 Windows 10 用戶端的本機電腦存放區中的用戶端憑證之後，使用下列範例來設定用戶端裝置通道。

1. 複製下列文字，並將它儲存成***devicecert.ps1***。

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
1. 複製下列文字，並將它儲存成***VPNProfile.xml***相同的資料夾中**devicecert.ps1**。 編輯下列文字，以符合您的環境。

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
1. 下載**PsExec**從[Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec)並將檔案解壓縮至**C:\PSTools**。
1. 從系統管理員命令提示字元中，啟動 PowerShell 執行：

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![Powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. 在 PowerShell 中，切換至資料夾所在**devicecert.ps1**並**VPNProfile.xml**所在，並執行下列命令：

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. 執行**rasphone**。

   ![rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. 尋求**MachineCertTest**項目，然後按一下**Connect**。

   ![連線](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. 如果連線成功，請將電腦重新開機。 通道會自動連接。

## <a name="cleanup"></a>清除

若要移除設定檔，請執行下列命令：

![清除](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>後續步驟

如需疑難排解，請參閱[Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
