---
title: 針對前端-Azure PowerShell 設定 web 應用程式防火牆速率限制規則
description: 了解如何設定現有的前端端點的速率限制規則。
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
ms.openlocfilehash: 903405c8fada6165b79e780a7828c6de3b95163e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478917"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>設定 web 應用程式防火牆速率限制規則使用 Azure PowerShell
Azure web 應用程式防火牆 (WAF) 速率限制規則的 Azure 大門會控制一分鐘期間允許從單一用戶端 IP 的要求數目。
本文說明如何設定 WAF 速率限制規則，以控制允許從單一用戶端所包含的 web 應用程式的要求數目 */promo*中使用 Azure PowerShell 的 URL。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件
開始設定速率限制原則之前，請設定您的 PowerShell 環境，並建立前端設定檔。
### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組 Cmdlet，它們會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 模型來管理 Azure 資源。 

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 依照指示在頁面上，使用您的 Azure 認證，登入並安裝 Az PowerShell 模組。

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用互動式登入對話方塊連線至 Azure
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

## <a name="define-url-match-conditions"></a>定義 url 比對條件
定義 URL 比對條件 （URL 會包含 /promo） 使用[新增 AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)。
下列範例會比對 */promo*的值為*RequestUri*變數：

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>建立自訂的速率限制規則
使用速率限制設定[新增 AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)。 在下列範例中，限制是設為 1000年。 在一分鐘內超過 1000年的促銷頁面從任何用戶端要求會遭到封鎖，直到下一分鐘會啟動。

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>設定安全性原則

使用 `Get-AzureRmResourceGroup` 尋找包含 Front Door 設定檔的資源群組名稱。 接下來，設定自訂的速率限制規則使用的 安全性原則[新增 AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)包含前門設定檔指定的資源群組中。

下列範例會使用資源群組名稱 myResourceGroupFD1  ，並假設您已使用[快速入門：建立 Front Door](quickstart-create-front-door.md) 一文中所提供的指示建立 Front Door 設定檔。

 使用[新增 AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>連結至大門前端主機的原則
安全性原則物件連結至現有的大門前端的主機，並更新大門屬性。 第一次擷取 大門物件使用[Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)命令。
接下來，設定 前端*WebApplicationFirewallPolicyLink*屬性設*resourceId*的 「 $ratePolicy"中先前的步驟使用建立[組 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令。 

下列範例會使用資源群組名稱 myResourceGroupFD1  ，並假設您已使用[快速入門：建立 Front Door](quickstart-create-front-door.md) 一文中所提供的指示建立 Front Door 設定檔。 此外，在下列範例中，取代 $frontDoorName 大門設定檔名稱。 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 您只需要設定*WebApplicationFirewallPolicyLink*屬性一次，以將安全性原則連結至前端的大門。 後續的原則更新會自動套用到前端中。

## <a name="next-steps"></a>後續步驟

- 深入了解[大門](front-door-overview.md) 


