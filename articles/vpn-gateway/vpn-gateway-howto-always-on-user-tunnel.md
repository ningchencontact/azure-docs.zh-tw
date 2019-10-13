---
title: 為您的 VPN 閘道設定 Always On VPN 使用者通道
description: 本文說明如何為您的 VPN 閘道設定 Always On VPN 使用者通道
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: 9aa1f951add5b79eab12f4957be05a42bbdd4434
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299919"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>設定 Always On VPN 使用者通道

Windows 10 VPN 用戶端 Always On 的新功能，就是維護 VPN 連線的能力。 使用 Always On 時，作用中的 VPN 設定檔可以根據觸發程式自動連線並保持連接狀態，例如使用者登入、網路狀態變更或裝置螢幕作用中。

您可以使用 Azure 虛擬網路閘道搭配 Windows 10 Always On，為 Azure 建立持續的使用者通道和裝置通道。 本文可協助您設定 Always On VPN 使用者通道。

Always On VPN 連線包含兩種通道類型之一：

* **裝置**通道：在使用者登入裝置之前，連線到指定的 VPN 伺服器。 登入前的連線案例和裝置管理使用裝置通道。

* **使用者**通道：只有在使用者登入裝置之後，才會連線。 藉由使用使用者通道，您可以透過 VPN 伺服器存取組織資源。

裝置通道和使用者通道的運作與它們的 VPN 設定檔無關。 它們可以同時連接，而且可以適當地使用不同的驗證方法和其他 VPN 設定。

在下列各節中，您會設定 VPN 閘道和使用者通道。

## <a name="step-1-configure-a-vpn-gateway"></a>步驟 1:設定 VPN 閘道

您可以遵循此[點對站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)文章中的指示，將 VPN 閘道設定為使用 IKEv2 和憑證型驗證。

## <a name="step-2-configure-a-user-tunnel"></a>步驟 2:設定使用者通道

1. 在 Windows 10 用戶端上安裝用戶端憑證，如此[點對站 VPN 用戶端一](point-to-site-how-to-vpn-client-install-azure-cert.md)文所示。 憑證必須位於 [目前使用者] 存放區中。

1. 遵循[設定 Windows 10 用戶端 ALWAYS ON VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)連線中的指示，透過 PowerShell、System Center Configuration Manager 或 Intune 設定 Always On VPN 用戶端。

### <a name="example-configuration-for-the-user-tunnel"></a>使用者通道的範例設定

設定虛擬網路閘道並在 Windows 10 用戶端的本機電腦存放區中安裝用戶端憑證之後，請使用下列範例來設定用戶端裝置通道：

1. 複製下列文字，並將它儲存為*usercert*：

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
1. 複製下列文字，並將它儲存為*VPNProfile*與*usercert*相同的資料夾中。 編輯下列文字以符合您的環境：

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

1. 在 PowerShell 中，切換至*usercert*和*VPNProfile*所在的資料夾，然後執行下列命令：

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. 在 [ **VPN 設定**] 底下，尋找 [ **UserTest** ] 專案，然後選取 **[連線]** 。

1. 如果連線成功，則您已成功設定 Always On 的使用者通道。

## <a name="clean-up-your-resources"></a>清除資源

若要移除設定檔，請執行下列動作：

1. 執行下列命令：

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 中斷連接連線，並清除 [**自動連接]** 核取方塊。

![清除](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>後續步驟

若要對任何可能發生的連線問題進行疑難排解，請參閱[Azure 點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。
