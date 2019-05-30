---
title: 設定 IP 限制規則與 web 應用程式防火牆規則的 Azure 大門
description: 了解如何設定現有的前端端點的 IP 位址限制 WAF 規則。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: dae2bb8ece9ef56c0999e0f89abbf6f8d8e950e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242932"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>設定 IP 限制規則與 web 應用程式防火牆的 Azure 大門 （預覽）
 這篇文章會示範如何使用 Azure CLI、 Azure PowerShell 或 Azure Resource Manager 範本在 Azure web 應用程式防火牆 (WAF) 的 IP 限制規則設定大門。

IP 位址型存取控制規則是自訂 WAF 規則，可讓您指定無類別網域間路由選擇 (CIDR) 格式的 IP 位址或 IP 位址範圍的清單來控制存取您的 web 應用程式。

根據預設，您的 web 應用程式是從網際網路存取。 如果您想要限制存取您 web 應用程式只對用戶端從清單中的已知的 IP 位址或 IP 位址範圍，您需要建立兩個 IP 的比對規則。 第一個 IP 的比對規則包含相符的值為 IP 位址的清單，並設定 「 允許 」 的動作。 第二個低優先順序，是藉由使用"All"運算子來封鎖所有其他 IP 位址，並將 「 區塊 」 的動作。 IP 限制規則套用之後，任何源自此允許清單外部位址的要求會收到 403 （禁止） 回應。  

> [!IMPORTANT]
> WAF IP 限制功能的 Azure 大門目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="configure-waf-policy-with-azure-cli"></a>使用 Azure CLI 設定 WAF 原則

### <a name="prerequisites"></a>必要條件
若要設定 IP 限制原則之前，設定 CLI 環境，並建立前端的設定檔。

#### <a name="set-up-azure-cli-environment"></a>設定 Azure CLI 環境
1. 安裝[Azure CLI](/cli/azure/install-azure-cli)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定 Azure CLI 以搭配您的帳戶。 選取 **試試**CLI 中的按鈕命令的。 選取**試試**叫用可讓您可以使用 Azure 帳戶登入 Cloud Shell。 Cloud shell 工作階段開始時，輸入`az extension add --name front-door`將大門擴充功能。
 2. 如果在本機使用 CLI 在 Bash 中，登入 Azure 中使用`az login`。

#### <a name="create-front-door-profile"></a>建立前端設定檔
請遵循[快速入門：建立前端的設定檔](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>建立 WAF 原則

建立具有 WAF 原則[az 網路 waf 原則建立](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create)命令。 在下列範例中，將 原則名稱*IPAllowPolicyExampleCLI*唯一原則名稱。

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>新增自訂 IP 存取控制規則

將自訂的 IP 存取控制規則新增至前一個步驟中所建立的 WAF 原則[az 網路 waf 原則自訂規則建立](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create)命令。 

在下列範例：
-  取代*IPAllowPolicyExampleCLI*與您稍早建立的唯一原則。
-  取代*ip 位址範圍-1*， *ip 位址範圍-2*與您自己的範圍。

首先，建立 IP 允許指定的地址的規則。

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
接下來，建立所有的 IP 規則具有區塊比先前的 IP 允許規則的優先順序較低。 取代*IPAllowPolicyExampleCLI*與您稍早建立的唯一原則。

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

### <a name="find-waf-policy-id"></a>尋找 WAF 原則識別碼
尋找具有 WAF 原則的識別碼[az 網路 waf 原則顯示](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show)命令。 取代*IPAllowPolicyExampleCLI*與您稍早建立的唯一原則。

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>連結 WAF 大門前端的主應用程式原則

設定前端*WebApplicationFirewallPolicyLink*識別碼的原則識別碼[az 網路大門更新](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update)命令。 取代*IPAllowPolicyExampleCLI*與您稍早建立的唯一原則。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
在此範例中，WAF 原則會套用至 FrontendEndpoints [0]。 您可能將 WAF 原則連結到任何您前端。
> [!Note]
> 您只需要設定**WebApplicationFirewallPolicyLink**屬性一次，以將 WAF 原則連結至前端的大門。 後續的原則更新會自動套用到前端中。

## <a name="configure-waf-policy-with-azure-powershell"></a>使用 Azure PowerShell 設定 WAF 原則

### <a name="prerequisites"></a>必要條件
若要設定 IP 限制原則之前，設定您的 PowerShell 環境，並建立前端的設定檔。

#### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 依照指示在頁面上，使用您的 Azure 認證，登入並安裝 Az PowerShell 模組。

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用互動式登入對話方塊連線到 Azure
```
Connect-AzAccount

```
安裝前端模組之前，請確定您有目前版本的 PowerShellGet 安裝。 執行下列命令，並重新開啟 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>安裝 Az.FrontDoor 模組 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>建立 Front Door 設定檔
請遵循[快速入門：建立前端的設定檔](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>定義 IP 比對條件
使用[新增 AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)命令來定義 「 IP 」 比對條件。 在下列範例中，取代*ip 位址範圍-1*， *ip 位址範圍-2*與您自己的範圍。

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
建立 IP 符合所有條件的規則
```powershell
  $IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>建立自訂 IP 允許規則
   使用[新增 AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject)命令來定義動作，並設定優先順序。 在下列範例中，將允許用戶端 Ip 的要求符合之清單。 

```powershell
  $IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
建立具有比先前的 IP 允許規則的優先順序較低的區塊所有 IP 規則。

```powershell
  $IPBlockAll = New-AzFrontDoorWafCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>設定 WAF 原則
使用 `Get-AzResourceGroup` 尋找包含 Front Door 設定檔的資源群組名稱。 接著，設定 WAF 原則 IP 規則使用的區塊[新增 AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>連結 WAF 大門前端的主應用程式原則

WAF 原則物件連結至現有的大門前端的主機，並更新大門屬性。 第一次擷取 大門物件使用[Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)。 接下來，設定 前端*WebApplicationFirewallPolicyLink*屬性的 resourceId *$IPAllowPolicyExamplePS*上一個步驟中建立[組 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令。

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 在此範例中，WAF 原則會套用至 FrontendEndpoints [0]。 您可能將 WAF 原則連結到任何您前端。您只需要設定*WebApplicationFirewallPolicyLink*屬性一次，以將 WAF 原則連結至前端的大門。 後續的原則更新會自動套用到前端中。


## <a name="configure-waf-policy-with-resource-manager-template"></a>使用 Resource Manager 範本來設定 WAF 原則
檢視建立自訂的 IP 限制規則的前端和 WAF 原則的範本[此處](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)。


## <a name="next-steps"></a>後續步驟

- 了解如何[建立前端的設定檔](quickstart-create-front-door.md)。
