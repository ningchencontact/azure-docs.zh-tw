---
title: 重寫 Azure 應用程式閘道中的 HTTP 標頭
description: 本文提供資訊來重寫 Azure 應用程式閘道的 HTTP 標頭如何使用 Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 405bc9aed4605e9728e112595f33c879bf55ec7f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005616"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>重新撰寫 HTTP 要求和回應標頭與 Azure 應用程式閘道-Azure PowerShell

這篇文章會示範如何使用 Azure PowerShell 來設定[應用程式閘道 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)重寫中的要求和回應的 HTTP 標頭。

> [!IMPORTANT]
> 自動調整規模和區域備援應用程式閘道 SKU 目前為公開預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

- 進行本教學課程時，您必須在本機執行 Azure PowerShell。 您必須安裝 Az 模組 1.0.0 版或更新版本。 請執行 `Import-Module Az`，然後執行 `Get-Module Az` 來尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。 驗證 PowerShell 版本之後，請執行 `Login-AzAccount` 以建立與 Azure 的連線。
- 您需要有 v1 SKU 不支援 SKU，因為標頭重寫功能的應用程式閘道 v2。 如果您還沒有 v2 SKU，建立[應用程式閘道 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)開始之前。

## <a name="what-is-required-to-rewrite-a-header"></a>什麼是需要重寫的標頭

若要設定 HTTP 標頭重寫，您將需要：

1. 建立重寫 HTTP 標頭所需的新物件：

   - **RequestHeaderConfiguration**：此物件可用來指定您想要重寫的要求標頭欄位，以及原始標頭在重寫後必須採用的新值。

   - **ResponseHeaderConfiguration**：此物件可用來指定您想要重寫的回應標頭欄位，以及原始標頭在重寫後必須採用的新值。

   - **ActionSet**：此物件包含以上所指定要求和回應標頭的設定。

   - **條件**：它是選擇性的設定。 如果新增重寫條件，則會評估的 HTTP (S) 要求和回應的內容。 是否與重寫條件相符的 HTTP (S) 要求或回應以決定来執行的重寫條件相關聯的重寫動作。 

     如果多個條件相關聯動作，則動作將會執行只有當符合所有條件，也就是，將執行邏輯 AND 運算。

   - **RewriteRule**： 包含多個重寫動作-重寫條件組合。

   - **RuleSequence**:這是選擇性的組態。 它可協助判斷在其中執行不同的重寫規則的順序。 重寫集合中有多個重寫規則時，這是很有幫助。 取得第一次執行的重寫規則，以較低的規則順序值。 如果您提供兩個重寫規則的相同規則順序執行的順序將會不具決定性的。

     如果您未明確指定 RuleSequence，就會設定預設值是 100。

   - **RewriteRuleSet**： 此物件包含多個重寫規則的要求路由規則建立關聯。

2. 您必須將路由規則 rewriteRuleSet。 這是因為的重寫組態附加至來源接聽程式，透過路由規則。 使用基本路由規則時，標頭重寫設定會與來源接聽程式相關聯，並且會是全域標頭重寫。 使用路徑型錄由規則時，會在 URL 路徑對應上定義標頭重寫設定。 因此，它只適用於網站的特定路徑區域。

您可以建立多個 http 標頭重寫集，而且每個重寫集可以套用至多個接聽程式。 不過，您可以套用只有一個重寫設為特定的接聽程式。

## <a name="sign-in-to-azure"></a>登入 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**指定 HTTP 標頭重寫規則設定**

在此範例中，我們將修改重新導向 URL 重寫在 http 回應的 location 標頭，每當 location 標頭包含"azurewebsites.net"的參考。 若要這樣做，我們將新增的條件來評估是否在回應中的 location 標頭使用模式包含 azurewebsites.net `(https?):\/\/.*azurewebsites\.net(.*)$`。 我們將使用`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`做為標頭值。 這會取代*azurewebsites.net*具有*contoso.com* location 標頭中。

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>擷取現有應用程式閘道的設定

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>擷取現有要求路由規則的設定

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>使用用於重寫 HTTP 標頭的設定來更新應用程式閘道

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>刪除重寫規則

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>後續步驟

若要了解深入了解完成的一些常見的所需的組態使用的情況下，請參閱[常見的標頭重寫案例](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。