---
title: 為 Azure 前端服務的 web 應用程式防火牆規則設定 IP 限制規則
description: 了解如何設定 web 應用程式防火牆規則以限制現有 Azure 前端服務端點的 IP 位址。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743153"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>設定 Azure 前端服務 web 應用程式防火牆 IP 限制規則
這篇文章會示範如何使用 Azure CLI、 Azure PowerShell 或 Azure Resource Manager 範本為 Azure 前端服務設定 IP 限制規則中的 web 應用程式防火牆 (WAF)。

IP 位址為基礎的存取控制規則是自訂 WAF 規則，可讓您控制存取您的 web 應用程式。 它會藉由指定無類別網域間路由選擇 (CIDR) 格式的 IP 位址或 IP 位址範圍的清單。

根據預設，您的 web 應用程式是從網際網路存取。 如果您想要限制的存取權的用戶端從已知的 IP 位址或 IP 位址範圍的清單，您必須建立兩個 IP 的比對規則。 第一個 IP 比對規則包含為符合值的 IP 位址的清單，並將動作設定為**允許**。 第二個，以較低的優先順序，使用封鎖所有其他 IP 位址**所有**運算子，並將動作設定為**區塊**。 套用 IP 限制規則之後，源自此允許清單外部位址的要求就會收到 403 禁止 回應。  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>使用 Azure CLI 設定 WAF 原則

### <a name="prerequisites"></a>必要條件
若要設定 IP 限制原則之前，設定 CLI 環境，並建立 Azure 前端服務設定檔。

#### <a name="set-up-the-azure-cli-environment"></a>設定 Azure CLI 環境
1. 安裝[Azure CLI](/cli/azure/install-azure-cli)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 選取 **試試**遵循，並再登入您的 Azure 帳戶開啟 Cloud Shell 工作階段中的 CLI 命令中的按鈕。 在工作階段啟動之後，請輸入`az extension add --name front-door`新增 Azure 前端服務延伸模組。
 2. 如果您要在本機使用 CLI，在 Bash 中，使用登入至 Azure `az login`。

#### <a name="create-an-azure-front-door-service-profile"></a>建立 Azure 前端服務設定檔
建立 Azure 前端服務設定檔中所述的指示[快速入門：建立高可用性的全域 web 應用程式的大門](quickstart-create-front-door.md)。

### <a name="create-a-waf-policy"></a>建立 WAF 原則

使用建立 WAF 原則[az 網路 waf 原則建立](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create)命令。 在範例中，如下所示，取代的原則名稱*IPAllowPolicyExampleCLI*唯一原則名稱。

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>新增自訂的 IP 存取控制規則

使用[az 網路 waf 原則自訂規則建立](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create)新增自訂 IP 存取控制規則 WAF 原則您剛才建立的命令。

在下列範例中：
-  取代*IPAllowPolicyExampleCLI*與您稍早建立的唯一原則。
-  取代*ip 位址範圍-1*， *ip 位址範圍-2*與您自己的範圍。

首先，建立 IP 允許指定的地址的規則。

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
接下來，建立**封鎖的全部**比上一個比低優先順序的規則**允許**規則。 同樣地，取代*IPAllowPolicyExampleCLI*在下列範例中使用您稍早建立的唯一原則。

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
    
### <a name="find-the-id-of-a-waf-policy"></a>尋找 WAF 原則識別碼 
使用找到的 WAF 原則的識別碼[az 網路 waf 原則顯示](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show)命令。 取代*IPAllowPolicyExampleCLI*在下列範例中使用您稍早建立的唯一原則。

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>WAF 原則連結至 Azure 的前端服務前端主機

將 Azure 前端服務設定*WebApplicationFirewallPolicyLink*使用的原則識別碼的識別碼[az 網路大門更新](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update)命令。 取代*IPAllowPolicyExampleCLI*與您稍早建立的唯一原則。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
在此範例中，WAF 原則會套用至**FrontendEndpoints [0]** 。 您可以在您的前端的任何連結 WAF 原則。
> [!Note]
> 您必須設定**WebApplicationFirewallPolicyLink**屬性一次，以將 WAF 原則連結至 Azure 的前端服務的前端。 後續的原則更新會自動套用到前端中。

## <a name="configure-a-waf-policy-with-azure-powershell"></a>使用 Azure PowerShell 設定 WAF 原則

### <a name="prerequisites"></a>必要條件
若要設定 IP 限制原則之前，設定您的 PowerShell 環境，並建立 Azure 前端服務設定檔。

#### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供一組使用的 cmdlet [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型來管理 Azure 資源。

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 遵循上的頁面，即可使用您的 Azure 認證，登入 PowerShell 的指示，然後再安裝 Az 模組。

1. 使用下列命令，連線到 Azure，並接著用來登入的互動式的對話方塊。
    ```
    Connect-AzAccount
    ```
 2. 安裝 Azure 前端服務模組之前，請確定您已安裝的 PowerShellGet 模組目前的版本。 執行下列命令，並再重新開啟 PowerShell。

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 使用下列命令，以安裝 Az.FrontDoor 模組。 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>建立 Azure 前端服務設定檔
建立 Azure 前端服務設定檔中所述的指示[快速入門：建立高可用性的全域 web 應用程式的大門](quickstart-create-front-door.md)。

### <a name="define-an-ip-match-condition"></a>定義 「 IP 」 比對條件
使用[新增 AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)命令來定義 「 IP 」 比對條件。
在下列範例中，取代*ip 位址範圍-1*， *ip 位址範圍-2*與您自己的範圍。    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
建立 IP*符合所有條件*規則，使用下列命令：
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>建立自訂 IP 允許規則

使用[新增 AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject)命令來定義動作，並設定優先順序。 在下列範例中，將允許用戶端 Ip 的要求符合之清單。

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
建立**封鎖的全部**具有比先前的 IP 的低優先順序的規則**允許**規則。
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>設定 WAF 原則
尋找包含 Azure 前端服務設定檔使用的資源群組名稱`Get-AzResourceGroup`。 接下來，設定 WAF 原則使用的 IP**封鎖的全部**使用的規則[新增 AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>WAF 原則連結至 Azure 的前端服務前端主機

WAF 原則物件連結到現有前端主應用程式和更新 Azure 前端服務的屬性。 首先，使用擷取 Azure 大門 Service 物件[Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)。 接下來，設定**WebApplicationFirewallPolicyLink**屬性的資源識別碼 *$IPAllowPolicyExamplePS*，建立在上一個步驟中，使用[組 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令。

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 在此範例中，WAF 原則會套用至**FrontendEndpoints [0]** 。 您可以在您的前端的任何連結 WAF 原則。 您必須設定**WebApplicationFirewallPolicyLink**屬性一次，以將 WAF 原則連結至 Azure 的前端服務的前端。 後續的原則更新會自動套用到前端中。


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>使用 Resource Manager 範本設定 WAF 原則
若要檢視建立自訂的 IP 限制規則 Azure 前端服務的原則和 WAF 原則範本，請前往[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)。


## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Azure 前端服務設定檔](quickstart-create-front-door.md)。
