---
title: Azure Web 應用程式防火牆 (WAF) 的自訂規則
description: 這篇文章概述的 web 應用程式防火牆 (WAF) Azure 應用程式閘道的自訂規則。
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 62259749e04d66d78206a0bba77ce88f2c08c82f
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688997"
---
# <a name="custom-rules-for-web-application-firewall"></a>Web 應用程式防火牆的自訂規則

> [!IMPORTANT]
> Azure 應用程式閘道 WAF 的自訂規則目前為公開預覽狀態。 **自訂的規則是僅適用於 WAF_v2 SKU**。
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 特定功能可能不受支援、功能可能受限，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 應用程式閘道 web 應用程式防火牆 (WAF) 會隨附預先設定的平台管理的規則集，可從許多不同類型的攻擊的防護。 這些攻擊，包括跨網站指令碼、 SQL 插入式攻擊，與其他人。 如果您是 WAF 的系統管理員，您可能想要撰寫您自己的規則，以加強核心規則集 (CRS) 規則。 您的規則可以封鎖或允許根據比對準則的要求的流量。

自訂規則可讓您建立您自己會評估每個要求通過 WAF 的規則。 這些規則會保留在受管理的規則集的優先順序高於其他規則。 自訂規則包含規則的名稱、 規則優先順序和比對條件的陣列。 如果符合這些條件，會執行動作 （若要允許或封鎖）。

例如，您可以封鎖來自 IP 位址範圍 192.168.5.4/24 中的所有要求。 在這項規則，是運算子*IPMatch*，matchValues 是 IP 位址範圍 (192.168.5.4/24)，該動作是封鎖的流量。 您也可以設定規則的名稱和優先順序。

自訂規則支援使用合併的邏輯，讓該位址，您的安全性需求的更進階的規則。 例如，(Condition 1**並**條件 2)**或**條件 3)。  此範例中表示，如果條件 1**並**滿足條件 2，則**或**WAF 如果符合條件 3 時，應該採取的自訂規則中指定的動作。

在相同的規則不同的比對條件一律更難處理使用**和**。 例如，封鎖流量來自特定 IP 位址，並只有在您使用特定瀏覽器。

如果您想要**或**兩個不同的條件，兩個條件必須在不同的規則。 如果他們使用特定的瀏覽器，例如封鎖來自特定 IP 位址] 或 [封鎖流量的流量。

> [!NOTE]
> WAF 自訂規則的數目上限為 100。 如需有關應用程式閘道限制的詳細資訊，請參閱 < [Azure 訂用帳戶和服務限制、 配額和條件約束](../azure-subscription-service-limits.md#application-gateway-limits)。

規則運算式也支援自訂規則，就像在 CRS 規則集。 這些範例，請參閱範例 3 和 5 中的[建立和使用自訂的 web 應用程式防火牆規則](create-custom-waf-rules.md)。

## <a name="allowing-vs-blocking"></a>允許與封鎖比較

允許和封鎖的流量是透過自訂規則。 例如，您可以封鎖來自某一範圍的 IP 位址的所有流量。 您可以讓另一項規則允許流量，如果要求來自特定的瀏覽器。

若要允許的項目，請確認`-Action`參數設定為**允許**。 若要封鎖的項目，請確認`-Action`參數設定為**區塊**。

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

上一個`$BlockRule`對應至 Azure Resource Manager 中的下列自訂規則：

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

此自訂規則包含名稱、 優先順序、 動作，以及比對條件所應採取的動作必須符合的陣列。 如需進一步說明這些欄位，請參閱下列欄位描述。 比方說的自訂規則，請參閱[建立和使用自訂的 web 應用程式防火牆規則](create-custom-waf-rules.md)。

## <a name="fields-for-custom-rules"></a>自訂規則的欄位

### <a name="name-optional"></a>名稱 [選用]

這是規則的名稱。 此名稱會出現在記錄檔。

### <a name="priority-required"></a>優先順序 [必要]

- 決定規則會進行評估的順序。 較低的值，舊版評估規則。
-必須是唯一之間所有的自訂規則。 將會評估優先順序 100 的規則，以防止優先順序 200 的規則之前。

### <a name="rule-type-required"></a>[必要] 的規則類型

目前，必須是**MatchRule**。

### <a name="match-variable-required"></a>比對變數 [必要]

必須是其中一個變數：

- RemoteAddr-IP 位址/hostname 的遠端電腦連接
- RequestMethod – HTTP 要求方法 （GET、 POST、 PUT、 DELETE 等等。）
- QueryString – 在 URI 中的變數
- PostArgs – POST 主體中傳送的引數
- RequestUri – 要求的 URI
- Webhookdata – 的要求標頭
- RequestBody – 要求的主體
- RequestCookies – 要求的 Cookie

### <a name="selector-optional"></a>[選用] 選取器

描述 matchVariable 集合的欄位。 例如，如果 matchVariable Webhookdata，選取器可以在*User-agent*標頭。

### <a name="operator-required"></a>運算子 [必要]

必須是下列運算子之一：

- IPMatch-比對變數是時，才使用*RemoteAddr*
- 等於 – 輸入大於 MatchValue 相同
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>否定條件 [選用]

取消目前的狀況。

### <a name="transform-optional"></a>[選用] 的轉換

嘗試的轉換，以便進行再比對名稱的字串清單。 這些可以是下列轉換：

- 小寫
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>符合值 [必要]

清單的值來比對，這可以被視為*或*' ed。 比方說，它可能是 IP 位址或其他字串。 值格式取決於先前的運算子。

### <a name="action-required"></a>[必要] 的動作

- 允許 – 授權的交易，略過所有後續的規則。 這表示指定的要求會加入至允許清單，而且一旦比對，要求就會停止進一步評估，並傳送至後端集區。 在允許清單上的規則未評估的任何進一步的自訂規則或受管理的規則。
- 區塊 – 封鎖交易為基礎*SecDefaultAction* （偵測/預防模式）。 就像允許動作，一旦要求會進行評估，並加入至封鎖清單，評估已經停止，並要求被封鎖。 符合相同的條件之後的任何要求將不會評估，而只是將會遭到封鎖。 
- 記錄檔-可讓寫入記錄檔，此規則，但可讓執行評估的其他規則。 後續的自訂規則的優先順序，後面接著的受管理的規則會進行評估。

## <a name="next-steps"></a>後續步驟

您會了解自訂規則之後,[建立您自己的自訂規則](create-custom-waf-rules.md)。
