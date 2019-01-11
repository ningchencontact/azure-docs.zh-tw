---
title: 重寫現有 Azure 應用程式閘道中的 HTTP 標頭
description: 本文提供有關如何使用 Azure PowerShell 來重寫現有「Azure 應用程式閘道」中 HTTP 標頭的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 8429618a945ec70c52b925887790aa469e23ef89
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730064"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>重寫現有應用程式閘道中的 HTTP 標頭

您可以使用 Azure PowerShell 來設定[重寫規則](rewrite-http-headers.md)，以重寫現有[自動調整規模和區域備援應用程式閘道 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 中的 HTTP 要求和回應標頭

> [!IMPORTANT] 
> 自動調整規模和區域備援應用程式閘道 SKU 目前為公開預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

在本教學課程中，您了解如何：

> [!div class="checklist"]
>
> * 擷取現有應用程式閘道的設定
> * 指定 HTTP 標頭重寫規則設定
> * 使用上述用於重寫 HTTP 標頭的設定來更新應用程式閘道

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

進行本教學課程時，您必須在本機執行 Azure PowerShell。 您必須安裝 Az 模組 1.0.0 版或更新版本。 請執行 `Import-Module Az`，然後執行 `Get-Module Az` 來尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 驗證 PowerShell 版本之後，請執行 `Login-AzAccount` 以建立與 Azure 的連線。

## <a name="sign-in-to-azure"></a>登入 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**指定 HTTP 標頭重寫規則設定**

設定重寫 HTTP 標頭所需的新物件：

- **RequestHeaderConfiguration**：此物件可用來指定您想要重寫的要求標頭欄位，以及原始標頭在重寫後必須採用的新值。
- **ResponseHeaderConfiguration**：此物件可用來指定您想要重寫的回應標頭欄位，以及原始標頭在重寫後必須採用的新值。
- **ActionSet**：此物件包含以上所指定要求和回應標頭的設定。 
- **RewriteRule**：此物件包含以上所指定的所有 *actionSet*。 
- **RewriteRuleSet**：此物件包含所有 *rewriteRule*，且將必須連結至要求路由規則 - 基本或路徑型。

```azurepowershell
$requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>擷取現有應用程式閘道的設定

```azurepowershell
$appgw1 = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>擷取現有要求路由規則的設定

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>使用用於重寫 HTTP 標頭的設定來更新應用程式閘道

```azurepowershell
$appgw1 = Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
$appgw2 = Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
$appgw3 =  Set-AzApplicationGateway -ApplicationGateway $appgw2
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立包含 URL 路徑型路由規則的應用程式閘道](./tutorial-url-route-powershell.md)
