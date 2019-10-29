---
title: Azure web 應用程式防火牆（WAF） v2 自訂規則
description: 本文概要說明 Azure 應用程式閘道中的 web 應用程式防火牆（WAF） v2 自訂規則。
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 2e96a2a2dd5504c906b5fb84b643467a83518f21
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027574"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>總覽： web 應用程式防火牆 v2 的自訂規則

Azure 應用程式閘道 web 應用程式防火牆（WAF） v2 隨附預先設定的平臺管理規則集，可提供各種不同類型攻擊的保護。 這些攻擊包括跨網站腳本、SQL 插入和其他。 如果您是 WAF 系統管理員，您可能會想要撰寫自己的規則來增強核心規則集規則。 您的規則可以根據符合條件來封鎖或允許要求的流量。

使用自訂規則，您可以建立自己的規則，其會針對通過 WAF 的每個要求進行評估。 這些規則的優先順序高於受管理規則集的其餘規則。 自訂規則包含規則名稱、規則優先順序，以及符合條件的陣列。 如果符合這些條件，則會採取動作來允許或封鎖。

例如，您可以建立規則來封鎖來自 192.168.5.4/24 範圍內 IP 位址的所有要求。 在此規則中，運算子為*IPMatch*， *matchValues*是 IP 位址範圍（192.168.5.4/24），而*動作*是用來封鎖流量。 您也可以設定規則的名稱和優先順序。

自訂規則支援使用複合邏輯來建立更先進的規則，以滿足您的安全性需求。 例如，「（條件 1*和*條件2）*或*條件3）」表示如果符合條件 1*和*條件2，*或*符合條件3，則 WAF 應採取自訂規則中指定的動作。

相同規則內的不同比對條件一律會使用*和*進行複合。 例如，使用*和*的規則可能會指定，以封鎖來自特定 IP 位址的流量，而且只有在使用特定瀏覽器的情況下。

如果您想要針對兩個不同的條件使用*或*，這兩個條件必須是不同的規則。 例如，使用*或*的規則可能會指定，以在使用特定瀏覽器時封鎖來自特定 IP 位址或封鎖流量的流量。

> [!NOTE]
> WAF 的自訂規則數目上限為100。 如需應用程式閘道限制的詳細資訊，請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](../azure-subscription-service-limits.md#application-gateway-limits)。

自訂規則也支援正則運算式，就像是在核心規則集中一樣。 如需這些規則的範例，請參閱[建立及使用自訂 web 應用程式防火牆規則](create-custom-waf-rules.md)中的「範例3」和「範例5」。

> [!NOTE]
> 在 v1 SKU WAF 中無法使用自訂規則。

## <a name="allowing-or-blocking-traffic"></a>允許或封鎖流量

使用自訂規則可讓您輕鬆允許或封鎖流量。 例如，您可以封鎖來自某個 IP 位址範圍的所有流量。 如果要求來自特定的瀏覽器，您可以建立另一個規則來允許流量。

若要允許某個專案，請確定 `-Action` 參數已設為 [**允許**]。 若要封鎖某個專案，請確定 `-Action` 參數設定為 [**封鎖**]，如下列程式碼所示：

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

前面的 `$BlockRule` 對應至 Azure Resource Manager 中的下列自訂規則：

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

此自訂規則包含名稱、優先順序、動作，以及必須符合才能進行動作的相符條件陣列。 如需自訂規則欄位的說明，請參閱下列各節。 如需自訂規則的範例，請參閱[建立和使用自訂 web 應用程式防火牆規則](create-custom-waf-rules.md)。

## <a name="custom-rule-fields"></a>自訂規則欄位

### <a name="name-optional"></a>名稱（選擇性）

這是規則的名稱。 名稱會出現在記錄中。

### <a name="priority-required"></a>優先順序（必要）

- 優先順序會決定評估規則的順序。 值愈低，先前的規則評估。 允許的範圍是從1到100。
- 優先順序在所有自訂規則中都必須是唯一的。 優先順序為40的規則會在優先順序為80的規則之前進行評估。

### <a name="rule-type-required"></a>規則類型（必要）

目前，規則類型必須是**MatchRule**。

### <a name="match-variable-required"></a>Match 變數（必要）

Match 變數必須是下列其中一項：

- RemoteAddr：遠端電腦連線的 IP 位址或主機名稱
- RequestMethod： HTTP 要求方法（GET、POST、PUT、DELETE 等等）。
- QueryString： URI 中的變數。
- PostArgs：在 POST 主體中傳送的引數。 只有在 Content-type 標頭設定為 "application/x-www-urlencoded" 和「多部分/表單資料」時，才會套用使用此比對變數的自訂規則。
- RequestUri：要求的 URI。
- RequestHeaders：要求的標頭。
- RequestBody：包含整個要求主體的變數。 只有當 Content-type 標頭設定為 "application/x-www-urlencoded" 時，才會套用使用此比對變數的自訂規則。 
- RequestCookies：要求的 cookie。

### <a name="selector-optional"></a>選取器（選擇性）

選取器會描述 matchVariable 集合的欄位。 例如，如果 matchVariable 是 "RequestHeaders"，則選取器可能在使用者代理程式標頭上。

### <a name="operator-required"></a>Operator （必要）

運算子必須是下列其中一項：

- IPMatch：只有在*RemoteAddr*相符的變數時，才會使用這個運算子。
- Equals：輸入與 MatchValue 相同。
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- 開始于
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>否定條件（選擇性）

將目前的條件否定。

### <a name="transform-optional"></a>轉換（選擇性）

在嘗試進行比對之前，具有要完成之轉換名稱的字串清單。 轉換包括：

- 小寫
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>符合值（必要）

MatchValues 欄位是要比對的值清單，可視為*或*' ed。 例如，值可以是 IP 位址或其他字串。 值格式取決於先前的運算子。

### <a name="action-required"></a>動作（必要）

[動作] 欄位提供下列選項： 

- 允許：授權交易，並略過所有後續的規則。 這表示指定的要求會新增至允許清單，並在符合後，要求會停止進一步評估並傳送至後端集區。 不會針對進一步的自訂規則或受管理規則評估允許清單上的規則。

- 封鎖：根據*SecDefaultAction* （偵測/預防模式）封鎖交易。 就像允許動作，評估要求並新增至封鎖清單之後，會停止評估並封鎖要求。 不會評估符合相同條件的後續要求。 它們只會被封鎖。 

- Log：讓規則寫入記錄檔，並讓其餘的規則執行以進行評估。 後續的自訂規則會依優先順序評估，後面接著受管理規則。

## <a name="next-steps"></a>後續步驟

既然您已瞭解自訂規則，您可以[建立自己的自訂規則](create-custom-waf-rules.md)。
