---
title: Azure Web 應用程式防火牆（WAF） v2 應用程式閘道的自訂規則
description: 本文提供 Azure 應用程式閘道上的 Web 應用程式防火牆（WAF） v2 自訂規則的總覽。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 844e24466e9a9b46be3212690767a408e75f234d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516613"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure 應用程式閘道上的 Web 應用程式防火牆 v2 自訂規則

Azure 應用程式閘道 Web 應用程式防火牆（WAF） v2 隨附預先設定的平臺管理規則集，可提供各種不同類型攻擊的保護。 這些攻擊包括跨網站腳本處理、SQL 插入和其他。 如果您是 WAF 系統管理員，您可能會想要撰寫自己的規則來增強核心規則集（CRS）規則。 您的規則可以根據符合條件來封鎖或允許要求的流量。

自訂規則可讓您建立自己的規則，以針對通過 WAF 的每個要求進行評估。 這些規則的優先順序高於受管理規則集的其餘規則。 自訂規則包含規則名稱、規則優先順序，以及符合條件的陣列。 如果符合這些條件，則會採取動作（以允許或封鎖）。

例如，您可以從範圍 192.168.5.4/24 中的 IP 位址封鎖所有要求。 在此規則中，運算子是*IPMatch*，MATCHVALUES 是 IP 位址範圍（192.168.5.4/24），而動作是用來封鎖流量。 您也可以設定規則的名稱和優先順序。

自訂規則支援使用複合邏輯來建立更先進的規則，以滿足您的安全性需求。 例如，（條件 1**和**條件2）**或**條件3）。  這個範例表示，如果符合條件 1**和**條件2，**或**符合條件3，則 WAF 應採取自訂規則中指定的動作。

相同規則內的不同相符條件一律會使用**和**進行複合。 例如，封鎖來自特定 IP 位址的流量，而且只有在使用特定的瀏覽器時，才會予以封鎖。

如果您想要**或**兩個不同的條件，這兩個條件必須是不同的規則。 例如，封鎖來自特定 IP 位址的流量，或在使用特定瀏覽器時封鎖流量。

> [!NOTE]
> WAF 的自訂規則數目上限為100。 如需應用程式閘道限制的詳細資訊，請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](../../azure-subscription-service-limits.md#application-gateway-limits)。

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

先前的 `$BlockRule` 對應至 Azure Resource Manager 中的下列自訂規則：

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

- 決定規則評估順序。 值愈低，先前的規則評估。 允許的範圍是從1-100。 
- 在所有自訂規則中必須是唯一的。 優先順序為40的規則會在優先順序為80的規則之前進行評估。

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
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- 開始于
- EndsWith
- Regex
- Geomatch （預覽）

### <a name="negate-condition-optional"></a>否定條件 [選擇性]

將目前的條件否定。

### <a name="transform-optional"></a>轉換 [選用]

在嘗試進行比對之前，具有轉換名稱的字串清單。 這些可以是下列轉換：

- 小寫
- Trim
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

## <a name="geomatch-custom-rules-preview"></a>Geomatch 自訂規則（預覽）

如果您使用 Geomatch 運算子，則選取器可以是下列兩位數的國家/地區代碼。 

|國碼 (地區碼) | 國家/地區名稱 |
| ----- | ----- |
| AD | 安道爾 |
| AE | 阿拉伯聯合大公國|
| AF | 阿富汗|
| AG | 安地卡及巴布達|
| AL | 阿爾巴尼亞|
| AM | 亞美尼亞|
| AO | 安哥拉|
| AR | 阿根廷|
| AS | 美屬薩摩亞|
| AT | 奧地利|
| AU | 澳大利亞|
| AZ | 亞塞拜然|
| BA | 波士尼亞赫塞哥維納|
| BB | 巴貝多|
| BD | 孟加拉|
| BE | 比利時|
| BF | 布吉納法索|
| BG | 保加利亞|
| BH | 巴林|
| BI | 蒲隆地|
| BJ | 貝南|
| BL | 聖巴瑟米|
| BN | 汶萊達魯薩蘭|
| BO | 玻利維亞|
| BR | 巴西|
| BS | 巴哈馬|
| BT | 不丹|
| BW | 波札那|
| BY | 白俄羅斯|
| BZ | 貝里斯|
| CA | 加拿大|
| CD | 剛果民主共和國|
| CF | 中非共和國|
| CH | 瑞士|
| CI | 象牙海岸|
| CL | 智利|
| CM | 喀麥隆|
| CN | 中國|
| CO | 哥倫比亞|
| CR | 哥斯大黎加|
| CU | 古巴|
| CV | 維德角|
| CY | 賽浦路斯|
| CZ | 捷克共和國|
| DE | 德國|
| DK | 丹麥|
| DO | 多明尼加共和國|
| DZ | 阿爾及利亞|
| EC | 厄瓜多|
| EE | 愛沙尼亞|
| EG | 埃及|
| ES | 西班牙|
| ET | 衣索比亞|
| FI | 芬蘭|
| FJ | 斐濟|
| FM | 密克羅尼西亞聯邦|
| FR | 法國|
| GB | 英國|
| GE | 喬治亞|
| GF | 法屬圭亞那|
| GH | 迦納|
| GN | 幾內亞|
| GP | 哥德洛普|
| GR | 希臘|
| GT | 瓜地馬拉|
| GY | 蓋亞那|
| HK | 香港特別行政區|
| HN | 宏都拉斯|
| HR | 克羅埃西亞|
| HT | 海地|
| HU | 匈牙利|
| ID | 印尼|
| IE | 愛爾蘭|
| IL | 以色列|
| IN | 印度|
| IQ | 伊拉克|
| IR | 伊朗伊斯蘭共和國|
| IS | 冰島|
| IT | 義大利|
| JM | 牙買加|
| JO | 約旦|
| JP | 日本|
| KE | 肯亞|
| KG | 吉爾吉斯|
| KH | 柬埔寨|
| KI | 吉里巴斯|
| KN | 聖克里斯多福及尼維斯|
| KP | 朝鮮民主主義人民共和國|
| KR | 大韓民國|
| KW | 科威特|
| KY | 開曼群島|
| KZ | 哈薩克|
| LA | 寮人民民主共和國|
| LB | 黎巴嫩|
| LI | 列支敦斯登|
| LK | 斯里蘭卡|
| LR | 賴比瑞亞|
| LS | 賴索托|
| LT | 立陶宛|
| LU | 盧森堡|
| LV | 拉脫維亞|
| LY | 利比亞 |
| MA | 摩洛哥|
| MD | 摩爾多瓦共和國|
| MG | 馬達加斯加|
| MK | 北馬其頓|
| ML | 馬利|
| MM | 緬甸|
| MN | 蒙古|
| MO | 澳門特別行政區|
| MQ | 馬丁尼克|
| MR | 茅利塔尼亞|
| MT | 馬爾他|
| MV | 馬爾地夫|
| MW | 馬拉威|
| MX | 墨西哥|
| MY | 馬來西亞|
| MZ | 莫三比克|
| NA | 納米比亞|
| NE | 尼日|
| NG | 奈及利亞|
| NI | 尼加拉瓜|
| NL | 荷蘭|
| 否 | 挪威|
| NP | 尼泊爾|
| NR | 諾魯|
| NZ | 紐西蘭|
| OM | 阿曼|
| PA | 巴拿馬|
| PE | 祕魯|
| PH | 菲律賓|
| PK | 巴基斯坦|
| PL | 波蘭|
| PR | 波多黎各|
| PT | 葡萄牙|
| PW | 帛琉|
| PY | 巴拉圭|
| QA | 卡達|
| RE | 留尼旺|
| RO | 羅馬尼亞|
| RS | 塞爾維亞|
| RU | 俄羅斯聯邦|
| RW | 盧安達|
| SA | 沙烏地阿拉伯|
| SD | 蘇丹|
| SE | 瑞典|
| SG | 新加坡|
| SI | 斯洛維尼亞|
| SK | 斯洛伐克|
| SN | 塞內加爾|
| SO | 索馬利亞|
| SR | 蘇利南|
| SS | 南蘇丹|
| SV | 薩爾瓦多|
| SY | 敘利亞阿拉伯共和國|
| SZ | 史瓦濟蘭|
| TC | 土克斯及開科斯群島|
| TG | 多哥|
| 二四 | 泰國|
| TN | 突尼西亞|
| TR | 土耳其|
| TT | 千里達及托巴哥|
| TW | 台灣|
| TZ | 坦尚尼亞聯合共和國|
| UA | 烏克蘭|
| UG | 烏干達|
| US | 美國|
| UY | 烏拉圭|
| UZ | 烏茲別克|
| VC | 聖文森及格瑞那丁|
| VE | 委內瑞拉|
| VG | 英屬維京群島|
| VI | 美屬維京群島|
| VN | 越南|
| ZA | 南非|
| ZM | 尚比亞|
| ZW | 辛巴威|

## <a name="next-steps"></a>後續步驟

瞭解自訂規則之後，請[建立您自己的自訂規則](create-custom-waf-rules.md)。
