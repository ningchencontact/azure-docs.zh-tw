---
title: 自訂 web 應用程式防火牆規則，在 Azure 應用程式閘道-PowerShell
description: 本文提供如何透過 PowerShell，在應用程式閘道中自訂 Web 應用程式防火牆規則的相關資訊。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 35114783f8f438a0890d59c05452f4f0b0dbb41b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618589"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>透過 PowerShell 自訂 Web 應用程式防火牆規則

Azure 應用程式閘道 Web 應用程式防火牆 (WAF) 提供 Web 應用程式的保護。 這些保護是由開放 Web 應用程式安全性專案 (OWASP) 的核心規則集 (CRS) 所提供。 某些規則可能會導致誤判，並封鎖真正的流量。 因此，應用程式閘道會提供功能以自訂規則群組與規則。 如需特定規則群組與規則的詳細資訊，請參閱 [Web 應用程式防火牆 CRS 規則群組與規則的清單](application-gateway-crs-rulegroups-rules.md)。

## <a name="view-rule-groups-and-rules"></a>檢視規則群組與規則

以下程式碼範例顯示如何檢視可在啟用 WAF 的應用程式閘道上設定的規則和規則群組。

### <a name="view-rule-groups"></a>檢視規則群組

下列範例顯示如何檢視規則群組：

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

以下輸出是上述範例的截斷回應：

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>停用規則

下列範例會停用應用程式閘道上的規則 `911011` 和 `911012`：

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>必要的規則

下列清單包含會造成封鎖的要求在防止模式 （在它們記錄為例外狀況的偵測模式） 中 WAF 的條件。 無法設定或停用這些：

* 無法剖析要求主體導致要求遭到封鎖，除非主體檢查已關閉 （XML、 JSON、 表單資料）
* 要求本文中 （的任何檔案） 的資料長度大於設定的限制
* （包括檔案） 的內文大於限制的要求
* WAF 引擎中發生內部錯誤

CRS 3.x 特定：

* 輸入異常分數超過閾值

## <a name="next-steps"></a>後續步驟

設定已停用的規則之後，您可以了解如何檢視 WAF 記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md#diagnostic-logging)。

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
