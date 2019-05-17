---
title: 使用自訂規則和預設集的大門-Azure PowerShell 的詭計設定 web 應用程式防火牆 (WAF) 原則
description: 了解如何設定 WAF 原則是由現有的前端端點的自訂和管理規則所組成。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 414869833b894e2688505a91fed8fafe0c912b73
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523746"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>設定 web 應用程式防火牆原則使用 Azure PowerShell
Azure web 應用程式防火牆 (WAF) 原則定義要求抵達大門時所需的檢查。
本文說明如何設定包含的一些自訂的規則，以及使用 Azure 受控預設詭計集已啟用 WAF 原則。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件
開始設定速率限制原則之前，請設定您的 PowerShell 環境，並建立前端設定檔。
### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 依照指示在頁面上，使用您的 Azure 認證，登入並安裝 Az PowerShell 模組。

#### <a name="sign-in-to-azure"></a>登入 Azure
```
Connect-AzAccount

```
在安裝 Front Door 模組之前，請確定您已安裝最新版的 PowerShellGet。 執行下列命令，然後重新開啟 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>安裝 Az.FrontDoor 模組 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>建立 Front Door 設定檔
請遵循[快速入門：建立前端的設定檔](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Http 參數為基礎的自訂規則

下列範例示範如何設定自訂規則，與使用的兩個比對條件[新增 AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)。 要求是從指定的網站訪客來源，所定義和查詢字串不包含 「 密碼 」。 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Http 要求方法為基礎的自訂規則
建立封鎖"PUT"的方法使用的規則[新增 AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) ，如下所示：

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>建立自訂規則，根據大小條件約束

下列範例會建立封鎖超過 100 個字元，使用 Azure PowerShell 的 url 要求的規則：
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>新增受管理預設規則集

下列範例會建立受管理預設規則設定使用 Azure PowerShell:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>設定安全性原則

使用 `Get-AzResourceGroup` 尋找包含 Front Door 設定檔的資源群組名稱。 接下來，使用在先前步驟中建立的規則與設定安全性原則[新增 AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)包含前門設定檔指定的資源群組中。

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>連結至大門前端主機的原則
安全性原則物件連結至現有的大門前端的主機，並更新大門屬性。 首先，使用您建立大門物件來擷取[Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)。
接下來，設定 前端*WebApplicationFirewallPolicyLink*屬性設*resourceId* "$myWAFPolicy$"中先前的步驟使用建立的[組 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

下列範例會使用資源群組名稱 myResourceGroupFD1，並假設您已使用[快速入門：建立 Front Door](quickstart-create-front-door.md) 一文中所提供的指示建立 Front Door 設定檔。 此外，在下列範例中，取代 $frontDoorName 大門設定檔名稱。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 您只需要設定*WebApplicationFirewallPolicyLink*屬性一次，以將安全性原則連結至前端的大門。 後續的原則更新會自動套用到前端中。

## <a name="next-steps"></a>後續步驟

- 深入了解[大門](front-door-overview.md) 
- 深入了解[WAF 大門](waf-overview.md)
