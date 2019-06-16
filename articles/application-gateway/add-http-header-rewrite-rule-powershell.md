---
title: 重寫 Azure 應用程式閘道中的 HTTP 標頭
description: 這篇文章提供如何使用 Azure PowerShell 來重寫 Azure 應用程式閘道的 HTTP 標頭資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947196"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>重新撰寫 HTTP 要求和回應標頭與 Azure 應用程式閘道-Azure PowerShell

這篇文章說明如何使用 Azure PowerShell 來設定[應用程式閘道 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)重寫要求和回應中的 HTTP 標頭的執行個體。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="before-you-begin"></a>開始之前

- 您需要執行 Azure PowerShell 在本機，才能完成這篇文章中的步驟。 您也需要有 Az 模組版本 1.0.0 或更新版本。 執行`Import-Module Az`，然後`Get-Module Az`來判斷您已安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。 驗證 PowerShell 版本之後，請執行 `Login-AzAccount` 以建立與 Azure 的連線。
- 您需要有應用程式閘道 v2 SKU 執行個體。 V1 SKU 中不支援重寫標頭。 如果您還沒有 v2 SKU，建立[應用程式閘道 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)在您開始前執行個體。

## <a name="create-required-objects"></a>建立所需的物件

若要設定 HTTP 標頭重寫，您需要完成下列步驟。

1. 建立物件所需的 HTTP 標頭重寫：

   - **RequestHeaderConfiguration**:用來指定您想要重寫要求標頭欄位和標頭的新值。

   - **ResponseHeaderConfiguration**:用來指定您想要重寫回應標頭欄位和標頭的新值。

   - **ActionSet**:包含要求和回應標頭先前指定的組態。

   - **條件**：選擇性的組態。 重寫條件評估結果 HTTP (S) 要求和回應的內容。 如果在 HTTP (S) 要求或回應符合重寫條件，就會發生重新寫入動作。

     如果您將多個條件與動作時，只在符合所有條件時，就會發生的動作。 換句話說，此作業是邏輯 AND 運算。

   - **RewriteRule**:包含多個重寫動作 / 重寫條件組合。

   - **RuleSequence**:選用設定，可協助判斷順序重寫規則執行。 當您在重寫組有多個重寫規則，此設定是很有幫助。 重寫規則具有較低的規則順序值會先執行。 如果您將相同的規則順序值指派給兩個重寫規則時，執行順序是不具決定性。

     如果您未明確指定 RuleSequence，會設定預設值是 100。

   - **RewriteRuleSet**:包含多個要求路由規則建立關聯的重寫規則。

2. 將 RewriteRuleSet 附加至路由規則。 重寫組態會附加至來源接聽程式，透過路由規則。 當您使用基本路由規則時，標頭重寫組態與來源接聽程式相關聯，並是全域的標頭重寫。 當您使用路徑型路由規則時，標頭重寫組態被定義 URL 路徑對應。 在此情況下，它只適用於站台的特定路徑區域。

您可以建立多個 HTTP 標頭重寫集，並套用設定多個接聽程式為每個重寫。 但您可以只有一個重寫設為特定的接聽程式。

## <a name="sign-in-to-azure"></a>登入 Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>指定 HTTP 標頭重寫規則設定

在此範例中，我們將修改重新導向 URL 重寫在 HTTP 回應的 location 標頭，每當 location 標頭包含 azurewebsites.net 的參考。 若要這樣做，我們將新增的條件來評估是否在回應中的 location 標頭包含 azurewebsites.net。 我們將使用此模式`(https?):\/\/.*azurewebsites\.net(.*)$`。 我們將使用和`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`做為標頭值。 此值將會取代*azurewebsites.net*具有*contoso.com* location 標頭中。

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>擷取您的應用程式閘道的組態

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>擷取您的要求路由規則組態

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>重新撰寫 HTTP 標頭的組態來更新應用程式閘道

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

若要深入了解如何設定一些常見的使用案例，請參閱[常見的標頭重寫案例](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。