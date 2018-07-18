---
title: 建立區域備援 Azure 應用程式閘道
description: 了解如何建立不需要在每個區域中使用個別閘道的區域備援應用程式閘道。
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937701"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>建立區域備援 Azure 應用程式閘道 - 個人預覽版

應用程式閘道區域備援平台是一個新的 SKU，提供現有應用程式閘道 SKU 所沒有的許多增強功能，包括：
- **區域復原** - 應用程式閘道部署現已可跨越多個可用性區域，而不需要使用流量管理員在每個區域中佈建及固定設置個別的應用程式閘道執行個體。 您可以選擇已部署應用程式閘道執行個體的單一區域或多個區域，以確保區域在失敗後具有復原能力。 應用程式的後端集區可以均等分散在可用性區域間。
- **效能提升**
- **靜態 VIP** - 應用程式閘道 VIP 現已可獨佔地支援靜態 VIP 類型。 這樣可以確保與應用程式閘道相關聯的 VIP 不會在重新啟動後變更。
- **客戶 SSL 憑證的 Key Vault 整合**
- **更快速的部署和更新時間**

> [!NOTE]
> 區域備援應用程式閘道目前處於個人預覽版狀態。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="supported-regions"></a>支援區域

區域備援應用程式閘道目前在美國東部 2 區域中受到支援。 近期將會新增更多區域。

## <a name="topologies"></a>拓撲

在目前版本中，您不再需要建立區域固定應用程式閘道以取得區域性備援能力。 相同的應用程式閘道部署現在可以跨越多個區域。

至少需要三個執行個體，以確保它們可以分散到所有的三個區域。 在新增更多執行個體時，應用程式閘道會將執行個體分散到各個區域。

先前的區域備援拓撲會如下圖所示：

![舊的備援拓撲](media/create-zone-redundant/old-redundant.png)

新的區域備援拓撲會如下圖所示：

![新的備援拓撲](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>部署

### <a name="prerequisites"></a>先決條件

目前，區域備援功能只能在個人預覽版中使用。 若要列入允許清單中，您必須傳送電子郵件至 appgwxzone@microsoft.com。 收到確認訊息後，您即可繼續執行後續步驟。 若要列入允許清單中，請在您的電子郵件中包含下列資訊：

- 訂用帳戶識別碼
- 區域名稱
- 所需的應用程式閘道概數

### <a name="resource-manager-template-deployment"></a>Resource Manager 範本部署

1. 確定您使用的訂用帳戶已如先前所述列入允許清單中。
2. 收到確認訊息後，請登入 Azure 帳戶，並選取適當的訂用帳戶 (如果您有多個訂用帳戶)。 確定您選取的訂用帳戶已在允許清單中。

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. 建立新的資源群組

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. 從 [GitHub](https://github.com/amitsriva/CrossZonePreview) 下載範本，並記下其儲存所在的資料夾。
5. 在您建立的資源群組中建立新的部署。 在部署之前，請適當地修改範本和參數檔案。 

   下圖說明您在 Azure 入口網站上可於何處擷取租用戶識別碼：

   ![入口網站中的租用戶識別碼](media/create-zone-redundant/tenant-id.png)

此範本會建立下列資源：

- **使用者指派的身分識別** - 此身分識別可用來讓應用程式閘道執行個體存取 Key Vault，以及擷取使用者儲存的憑證。
- **Key Vault** – 使用者憑證儲存所在的 Key Vault。 這也可以是現有的 Key Vault。
- **密碼** – 儲存在 Key Vault 中的私密金鑰。
- **存取原則** – 在 Key Vault 上套用的存取原則，可授與「使用者指派的身分識別」的使用權限，讓應用程式閘道部署能夠擷取使用者憑證。
- **公用 IP 位址** – 保留用來存取應用程式閘道的 IP 位址。 此 IP 位址在應用程式閘道的生命週期內不會變更。
- **網路安全性群組** – 在應用程式閘道子網路上自動建立的 NSG，可在已設定的接聽程式上開啟連接埠流量。 相較於隱含設定此 NSG 的舊版 SKU，新的 SKU 會明確建立此群組並加以管理。
- **虛擬網路** – 應用程式閘道和應用程式部署所在的 vnet。
- **應用程式閘道** – 在所需的區域中以執行個體建立應用程式閘道。 依預設會選取所有區域 (1,2,3)。 SKU 名稱變更為 *Standard_v2*。 此 SKU 名稱日後有可能會變更。 目前，自動調整組態具有設定為所需執行個體數目的最小值和最大值。 自動調整啟用後，您可以調整這些值。

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>PowerShell 部署

1. 確定使用的訂用帳戶已如先前必要條件中的說明列入允許清單中。
2. 從 [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi) 下載並安裝個人 PowerShell MSI。
3. 從預覽版註冊確認電子郵件中所述的位置下載個人 PowerShell zip 檔案。 將檔案解壓縮到您的磁碟機，並記下位置。
4. 在預覽版啟用後，請先載入預覽模組，再登入您的帳戶：

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. 登入 Azure 帳戶，並選取所需的訂用帳戶 (如果您有多個訂用帳戶)。 確定您選取的是已在允許清單中的適當訂用帳戶。
5. 執行下列命令以建立一般常數，並在其中包含大部分所建立之實體的名稱。 

   視需要修改命名喜好設定的項目。

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. 建立資源群組：

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. 建立使用者指派的身分識別，用來提供應用程式閘道從 Key Vault 擷取憑證的存取權。

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. 建立用來儲存憑證的 Key Vault：

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. 將憑證上傳至 Key Vault 作為密碼：

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. 使用「使用者指派的身分識別」，將存取原則指派給 Key Vault。 這可讓應用程式閘道執行個體存取 Key Vault 密碼：

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. 建立網路安全性群組 (NSG)，以允許在建立新接聽程式的連接埠上存取應用程式閘道子網路。

    例如，對於預設連接埠上的 HTTP/HTTPS，NSG 會允許對 80、443 的輸入存取，而對於管理作業則允許 65200-65535。

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. 建立 VNet 和子網路：

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. 建立保留/靜態類型的公用 IP 位址：

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. 建立應用程式閘道：

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. 擷取已建立的應用程式閘道的公用 IP 位址：

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>常見問題集

-  預覽版的應用程式閘道是否會收費？

   預覽期間無須付費。 應用程式閘道以外的資源則會收費，例如 Key Vault、虛擬機器等。
- 在哪些區域中可使用預覽版？

   預覽版目前可在美國東部 2 區域使用。 近期將會新增更多區域。
- 預覽版是否支援入口網站？

   否，在個人預覽版期間，支援僅限於個人 PowerShell 模組和 Resource Manager 範本。

- 個人預覽版期間是否支援生產工作負載？

   否，個人預覽版期間未提供 SLA 或支援。 不建議您在預覽期間加入生產工作負載。 預覽版的支援僅限於使用電子郵件別名與產品群組直接互動。

- 如何回報問題？

   個人預覽版可能包含 Bug，而且可能會有頻繁的程式碼部署。 請使用支援別名 appgwxzone@microsoft.com 來回報問題及取得協助。

## <a name="known-issues-and-limitations"></a>已知問題與限制


|問題  |詳細資料  |
|---------|---------|---------|
|計費     |目前不收費|
|診斷記錄 (非計量)     |目前不會顯示效能和要求/回應記錄|
|入口網站/CLI/SDK     |不支援入口網站、CLI 或 SDK。 不可使用入口網站來發行預覽閘道的更新。|
|透過範本的更新偶爾會失敗     |這是因為與 KeyVault 存取原則間有競爭情形|在 Key Vault 和使用者指派的身分識別建立後，即可從範本中加以移除，且範本中只需要對密碼和身分識別的參考。|
|自動調整     |目前不支援自動調整|
|WAF     |目前不支援 WAF。|
|使用者提供的憑證和動態 VIP     |新的模型中不支援這些項目。 請使用 Key Vault 來儲存憑證和靜態 VIP。|
|舊版和預覽版的應用程式閘道使用相同的子網路     |具有現有應用程式閘道 (舊模型) 的子網路無法用於個人預覽版。|
|以 HTTP/2、FIPS 模式、WebSocket、Azure Web 應用程式作為後端     |目前不支援 |


## <a name="support-and-feedback"></a>支援與意見反應

如需支援與意見反應，請連絡 appgwxzone@microsoft.com。應用程式閘道產品群組樂意聆聽您針對增強功能的意見反應，並提供必要的指引。

## <a name="next-steps"></a>後續步驟

了解其他應用程式閘道功能：

- [什麼是 Azure 應用程式閘道？](overview.md)