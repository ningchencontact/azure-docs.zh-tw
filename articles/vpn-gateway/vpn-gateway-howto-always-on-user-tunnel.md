---
title: 設定 VPN 閘道的 Always On VPN 通道
description: 設定 VPN 閘道 Always On 使用者 VPN 通道的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846468"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>設定 Always On VPN 使用者通道

Windows 10 虛擬私人網路 (VPN) 用戶端的其中一項新功能就是維護 VPN 連線的能力。 Always On 是一項 Windows 10 功能, 可讓使用中的 VPN 設定檔自動連接, 並根據觸發程式保持線上狀態, 也就是使用者登入、網路狀態變更或裝置螢幕作用中。

Azure 虛擬網路閘道可以與 Windows 10 Always On 搭配使用, 以建立持續的使用者通道, 以及 Azure 的裝置通道。 本文將協助您設定 Always On VPN 使用者通道。

Always On VPN 連接包含兩種類型的通道:

* **裝置**通道會在使用者登入裝置之前, 連線到指定的 VPN 伺服器。 登入前的連線案例和裝置管理用途是使用裝置通道。

* 使用者通道只會在使用者登入裝置之後連線。 使用者通道可讓使用者透過 VPN 伺服器存取組織資源。

裝置通道和使用者通道都是獨立運作的 VPN 設定檔。 它們可以同時連接, 而且可以適當地使用不同的驗證方法和其他 VPN 設定。

## <a name="1-configure-the-gateway"></a>1.設定閘道

將 VPN 閘道設定為使用 IKEv2 和以憑證為基礎的驗證, 並使用這個[點對站文章](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。

## <a name="2-configure-the-user-tunnel"></a>2.設定使用者通道

1. 在 Windows 10 用戶端上安裝用戶端憑證, 如此[點對站 VPN 用戶端一文](point-to-site-how-to-vpn-client-install-azure-cert.md)所示。 憑證必須位於目前的使用者存放區
2. 使用[這些指示](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections), 透過 POWERSHELL、SCCM 或 Intune 設定 Always On VPN 用戶端。

### <a name="configuration-example-for-user-tunnel"></a>使用者通道的設定範例

設定虛擬網路閘道並在 Windows 10 用戶端的本機電腦存放區中安裝用戶端憑證之後, 請使用下列範例來設定用戶端裝置通道。

1. 複製下列文字，並將它儲存為***usercert***。

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
1. 複製下列文字，並將它儲存為***VPNProfile***與**usercert**相同的資料夾。 編輯下列文字以符合您的環境。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. 以系統管理員身分執行 PowerShell。

1. 在 PowerShell 中，切換至**usercert**和**VPNProfile**所在的資料夾，然後執行下列命令：

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. 查看 [VPN 設定] 底下的。

1. 尋找**UserTest**專案，然後按一下 **[連接]** 。

1. 如果連線成功，則您已成功設定「永遠開啟」使用者通道。

## <a name="cleanup"></a>清除

若要移除設定檔, 請執行下列命令:

1. 中斷連線連線並取消核取 [自動連線]

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![清除](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>後續步驟

如需疑難排解, 請參閱[Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
