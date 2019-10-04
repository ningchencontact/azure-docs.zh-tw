---
title: Azure Web 應用程式防火牆（WAF） v2 自訂規則
description: 本文概要說明 Azure 應用程式閘道中的 Web 應用程式防火牆（WAF） v2 自訂規則。
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 154317e558c2c9a22f569f569684cced467900d5
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937470"
---
# <a name="custom-rules-for-web-application-firewall-v2"></a>Web 應用程式防火牆 v2 的自訂規則

Azure 應用程式閘道 Web 應用程式防火牆（WAF） v2 隨附預先設定的平臺管理規則集，可提供各種不同類型攻擊的保護。 這些攻擊包括跨網站腳本處理、SQL 插入和其他。 如果您是 WAF 系統管理員，您可能會想要撰寫自己的規則來增強核心規則集（CRS）規則。 您的規則可以根據符合條件來封鎖或允許要求的流量。

自訂規則可讓您建立自己的規則，以針對通過 WAF 的每個要求進行評估。 這些規則的優先順序高於受管理規則集的其餘規則。 自訂規則包含規則名稱、規則優先順序，以及符合條件的陣列。 如果符合這些條件，則會採取動作（以允許或封鎖）。

例如，您可以從範圍 192.168.5.4/24 中的 IP 位址封鎖所有要求。 在此規則中，運算子是*IPMatch*，MATCHVALUES 是 IP 位址範圍（192.168.5.4/24），而動作是用來封鎖流量。 您也可以設定規則的名稱和優先順序。

自訂規則支援使用複合邏輯來建立更先進的規則，以滿足您的安全性需求。 例如，（條件 1**和**條件2）**或**條件3）。  這個範例表示，如果符合條件 1**和**條件2，**或**符合條件3，則 WAF 應採取自訂規則中指定的動作。

相同規則內的不同相符條件一律會使用**和**進行複合。 例如，封鎖來自特定 IP 位址的流量，而且只有在使用特定的瀏覽器時，才會予以封鎖。

如果您想要**或**兩個不同的條件，這兩個條件必須是不同的規則。 例如，封鎖來自特定 IP 位址的流量，或在使用特定瀏覽器時封鎖流量。

> [!NOTE]
> WAF 的自訂規則數目上限為100。 如需應用程式閘道限制的詳細資訊，請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](../azure-subscription-service-limits.md#application-gateway-limits)。

自訂規則也支援正則運算式，就像在 CRS 規則集內一樣。 如需相關範例，請參閱[建立和使用自訂 web 應用程式防火牆規則](create-custom-waf-rules.md)中的範例3和5。

## <a name="allowing-vs-blocking"></a>允許與封鎖

使用自訂規則可讓您輕鬆地允許和封鎖流量。 例如，您可以封鎖來自某個 IP 位址範圍的所有流量。 如果要求來自特定的瀏覽器，您可以建立另一個規則來允許流量。

若要允許某個專案，請確定 `-Action` 參數已設為 [**允許**]。 若要封鎖某個專案，請確定 `-Action` 參數設定為 [**封鎖**]。

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

先前的 `$BlockRule` 會對應到 Azure Resource Manager 中的下列自訂規則：

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

此自訂規則包含 [名稱]、[優先順序]、[動作]，以及必須符合才能進行動作的相符條件陣列。 如需這些欄位的進一步說明，請參閱下欄欄位描述。 如需範例自訂規則，請參閱[建立和使用自訂 web 應用程式防火牆規則](create-custom-waf-rules.md)。

## <a name="fields-for-custom-rules"></a>自訂規則的欄位

### <a name="name-optional"></a>名稱 [選用]

這是規則的名稱。 此名稱會出現在記錄中。

### <a name="priority-required"></a>優先順序 [必要]

- 決定評估規則的順序。 值愈低，先前的規則評估。 允許的範圍是從1-100。 
- 在所有自訂規則之間必須是唯一的。 優先順序為40的規則會在優先順序80的規則之前評估。

### <a name="rule-type-required"></a>規則類型 [必要]

目前，必須是**MatchRule**。

### <a name="match-variable-required"></a>Match 變數 [required]

必須是其中一個變數：

- RemoteAddr –遠端電腦連線的 IP 位址/主機名稱
- RequestMethod – HTTP 要求方法（GET、POST、PUT、DELETE 等等）。
- QueryString – URI 中的變數
- PostArgs –在 POST 主體中傳送的引數。 只有當 ' Content-type ' 標頭設定為 ' application/x-www-urlencoded ' 和 ' 多部分/表單資料 ' 時，才會套用使用此比對變數的自訂規則。
- RequestUri –要求的 URI
- RequestHeaders –要求的標頭
- RequestBody –這包含整個要求主體。 只有當 ' Content-type ' 標頭設為 ' application/x-www-forms-urlencoded ' 時，才會套用使用此比對變數的自訂規則。 
- RequestCookies –要求的 Cookie

### <a name="selector-optional"></a>選取器 [選用]

描述 matchVariable 集合的欄位。 例如，如果 matchVariable 是 RequestHeaders，則選取器可能在*使用者代理程式*標頭上。

### <a name="operator-required"></a>運算子 [必要]

必須是下列其中一個運算子：

- IPMatch-僅在 Match 變數為*RemoteAddr*時使用
- Equals –輸入與 MatchValue 相同
- 包含
- 小於
- 大於
- LessThanOrEqual
- GreaterThanOrEqual
- 開始于
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>否定條件 [選擇性]

將目前的條件否定。

### <a name="transform-optional"></a>轉換 [選用]

在嘗試進行比對之前，具有轉換名稱的字串清單。 這些可以是下列轉換：

- 小寫
- 修剪
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>符合值 [必要]

要比對的值清單，可視為*或*' ed。 例如，它可以是 IP 位址或其他字串。 值格式取決於先前的運算子。

### <a name="action-required"></a>動作 [必要]

- Allow –授權交易，略過所有後續的規則。 這表示指定的要求會新增至允許清單，一旦相符，要求就會停止進一步評估並傳送至後端集區。 不會針對任何進一步的自訂規則或受管理規則評估允許清單上的規則。
- Block –封鎖以*SecDefaultAction* （偵測/預防模式）為基礎的交易。 就像「允許」動作，一旦評估要求並將其加入至封鎖清單之後，就會停止評估並封鎖要求。 任何符合相同條件的要求都不會進行評估，而且只會被封鎖。 
- Log –可讓規則寫入至記錄檔，但可讓其餘規則執行評估。 後續的自訂規則會依優先順序評估，後面接著受管理規則。

## <a name="next-steps"></a>後續步驟

瞭解自訂規則之後，請[建立您自己的自訂規則](create-custom-waf-rules.md)。
