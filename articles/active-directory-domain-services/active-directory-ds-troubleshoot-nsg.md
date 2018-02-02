---
title: "Azure Active Directory Domain Services：針對網路安全性群組設定進行疑難排解 | Microsoft Docs"
description: "針對 Azure AD Domain Services 的 NSG 設定進行疑難排解"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure AD Domain Services - 針對網路安全性群組設定進行疑難排解



## <a name="aadds104-network-error"></a>AADDS104：網路錯誤

**警示訊息：***Microsoft 無法觸達此受控網域的網域控制站。如果虛擬網路上設定的網路安全性群組 (NSG) 封鎖受控網域的存取，就可能發生這種情況。另一個可能的原因，是使用者定義的路由封鎖了來自網際網路的連入流量。*

Azure AD Domain Services 網路錯誤最常見的原因可歸咎於 NSG 設定不正確。 若要確保 Microsoft 能夠服務並維護受控網域，您必須使用網路安全性群組 (NSG) 來允許子網路內[特定連接埠](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)的存取。 如果這些連接埠遭到封鎖，Microsoft 就無法存取其所需的資源，而可能對您的服務有所危害。 在建立 NSG 時，請將這些連接埠保持開啟，以確保服務不中斷。

## <a name="sample-nsg"></a>NSG 範例
下表描述的 NSG 範例，能讓受控網域保持安全，同時允許 Microsoft 監視、管理及更新資訊。

![NSG 範例](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services 需要不受限制的輸出存取。 建議您不要對 NSG 建立任何其他輸出規則。

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>使用 Azure 入口網站對網路安全性群組新增規則
如果您不想使用 PowerShell，則可使用 Azure 入口網站，手動對 NSG 新增單一規則。 請遵循下列步驟，在網路安全性群組中建立規則。

1. 在 Azure 入口網站中，瀏覽至 [網路安全性群組](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 頁面
2. 從資料表選擇與網域相關聯的 NSG。
3. 在左側導覽的 [設定] 下，按一下 [輸入安全性規則] 或 [輸出安全性規則]。
4. 按一下 [新增] 並填入資訊來建立規則。 按一下 [SERVICEPRINCIPAL] 。
5. 在規則資料表中尋找您的規則，確認規則已建立。


## <a name="create-a-default-nsg-using-powershell"></a>使用 PowerShell 來建立預設 NSG

前述步驟可讓您使用 PowerShell 建立新的 NSG，以將執行 Azure AD Domain Services 所需的所有連接埠保持開啟，同時拒絕任何其他不必要的存取。


這個解決方案需要您安裝及執行 [Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0)。

1. 連線到 Azure AD 目錄。

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. 登入 Azure 訂用帳戶。

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. 建立具有三個規則的 NSG。 下列指令碼會定義 NSG 的三個規則，從而允許系統存取要執行 Azure AD Domain Services 所需使用的連接埠。 接著，此指令碼會建立包含這些規則的新 NSG。 您可以使用相同的格式，適當地新增其他規則。

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. 最後，此指令碼會將 NSG 與您選擇的 VNet 和子網路建立關聯。

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>完整指令碼

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>此預設 NSG 不會鎖定對於安全 LDAP 所用之連接埠的存取。 若要了解如何建立此連接埠的規則，請瀏覽[這篇文章](active-directory-ds-troubleshoot-ldaps.md)。
>

## <a name="contact-us"></a>與我們連絡
請連絡 Azure Active Directory Domain Services 產品小組， [分享意見或尋求支援](active-directory-ds-contact-us.md)。
