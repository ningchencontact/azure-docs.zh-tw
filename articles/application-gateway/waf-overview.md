---
title: Azure 應用程式閘道 web 應用程式防火牆簡介
description: 本文提供適用於應用程式閘道的 Web 應用程式防火牆 (WAF) 的概觀
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 9c2759222198f5df682d9e7a5363c0d9679e0fad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991409"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Azure 應用程式閘道 web 應用程式防火牆

Azure 應用程式閘道提供 web 應用程式防火牆 (WAF) 可提供您的 web 應用程式，免於遭遇常見攻擊和弱點的集中式的保護。 利用常見已知的弱點的惡意攻擊會越來越多的目標 web 應用程式。 SQL 資料隱碼和跨網站指令碼是最常見的攻擊。

防止這類攻擊，應用程式程式碼中的是一項挑戰。 它可以要求嚴格的維護、 修補和監視的應用程式拓撲的多個層級。 集中式的 web 應用程式防火牆可協助簡化安全性管理更簡單。 WAF 也可讓應用程式系統管理員更好的保證，防範潛在威脅及侵入。

WAF 解決方案可以藉由集中修補已知的弱點，而不是保護每個個別的 web 應用程式回應更快的安全性威脅。 現有的應用程式閘道可輕易轉換到火災牆上啟用應用程式閘道。

應用程式閘道 WAF 根據[核心規則集 (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 或 2.2.9 從 Open Web Application Security Project (OWASP)。 WAF 會自動更新以包含對新的弱點的保護，不需要的其他設定。

![應用程式閘道 WAF 圖表](./media/waf-overview/WAF1.png)

應用程式閘道的運作方式做為應用程式傳遞控制器 (ADC)。 它提供了安全通訊端層 (SSL) 終止、 cookie 型工作階段同質、 循環配置資源負載分配、 內容架構路由、 代管多個網站和安全性增強功能的能力。

應用程式閘道的安全性增強功能包括 SSL 原則管理以及端對端 SSL 支援。 應用程式的安全性更強的應用程式閘道的 WAF 整合。 組合可保護您的 web 應用程式，抵禦常見弱點。 並提供簡單設定中央位置，以便管理。

## <a name="benefits"></a>優點

本章節描述其 WAF 和應用程式閘道提供的核心優勢。

### <a name="protection"></a>保護

* 保護您的 web 應用程式從 web 弱點和攻擊，而不修改後端的程式碼。

* 在此同時保護多個 web 應用程式。 應用程式閘道的執行個體可以裝載的 web 應用程式防火牆所保護的最多 100 個網站。

### <a name="monitoring"></a>監視

* 使用即時 WAF 記錄，以監視 web 應用程式的攻擊。 記錄檔整合[Azure 監視器](../monitoring-and-diagnostics/monitoring-overview.md)追蹤 WAF 警示並輕鬆地監視趨勢。

* 應用程式閘道 WAF 會與 Azure 資訊安全中心整合。 資訊安全中心提供集中檢視所有 Azure 資源的安全性狀態。

### <a name="customization"></a>自訂

* 您可以自訂 WAF 規則與規則群組，以符合您的應用程式的需求並消除誤判。

## <a name="features"></a>功能

- SQL 插入式攻擊的保護。
- 跨網站指令碼的保護。
- 針對其他常見的 web 攻擊，例如命令插入、 HTTP 要求走私、 HTTP 回應分割和遠端檔案引入的保護。
- 防範 HTTP 通訊協定違規。
- 防範 HTTP 通訊協定異常狀況，例如遺漏主機使用者代理程式，並接受標頭。
- 防範 bot、 編目程式和掃描器。
- 偵測一般應用程式錯誤組態 （例如，Apache 和 IIS）。
- 可設定的要求大小限制與下限。
- 排除清單可讓您略過從 WAF 評估特定要求屬性。 常見範例是用來驗證或密碼欄位的 Active Directory 插入語彙基元。

### <a name="core-rule-sets"></a>核心規則集

應用程式閘道支援兩個規則集，CRS 3.0 和 CRS 2.2.9。 這些規則會將您的 web 應用程式防止惡意活動。

應用程式閘道 WAF 已預先使用 CRS 3.0 預設設定。 但是，您可以選擇改為使用 CRS 2.2.9。 CRS 3.0 供應項目減少誤判，相較於 CRS 2.2.9。 您也可以[自訂規則以符合您的需求](application-gateway-customize-waf-rules-portal.md)。

WAF 可保護對以下的 web 弱點：

- SQL 資料隱碼攻擊
- 跨網站指令碼攻擊
- 其他常見的攻擊，例如命令插入、 HTTP 要求走私、 HTTP 回應分割及遠端檔案引入
- HTTP 通訊協定違規
- HTTP 通訊協定異常狀況，例如遺漏主機使用者代理程式和 accept 標頭
- Bot、 編目程式和掃描器
- 常見的應用程式錯誤組態 （例如，Apache 和 IIS）

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 會包含 13 個規則群組下, 表所示。 每個群組包含多個規則，您可以停用。

|規則群組|描述|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|鎖定方法 (PUT、 PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|針對連接埠和環境掃描器保護|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|防範通訊協定和編碼問題|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|防止標頭插入式攻擊、 要求走私和回應分割|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|保護檔案和路徑攻擊|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|保護遠端檔案包含 (RFI) 攻擊|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|重新保護執行遠端程式碼攻擊|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|防禦 PHP 插入式攻擊|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|防止跨網站指令碼攻擊|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|防禦 SQL 插入式攻擊|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|工作階段 fixation 攻擊保護|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 中將包含 10 個規則群組下, 表所示。 每個群組包含多個規則，您可以停用。

|規則群組|描述|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|防範通訊協定違規 （例如無效的字元或 GET 要求內文）|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|防範不正確的標頭資訊|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|防止引數或超出限制的檔案|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|保護不受限制的方法、 標頭，以及檔案類型|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|防禦 web 編目程式和掃描器|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|防禦一般攻擊 （例如工作階段 fixation、 遠端檔案引入和 PHP 插入式攻擊）|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|防禦 SQL 插入式攻擊|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|防止跨網站指令碼攻擊|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|防禦路徑周遊攻擊|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|防禦特洛伊木馬後門程式|

### <a name="waf-modes"></a>WAF 模式

應用程式閘道 WAF 可以設定為在下列兩種模式中執行：

* **偵測模式**:會監視並記錄所有威脅警示。 您開啟應用程式閘道中的記錄診斷**診斷**一節。 您也必須確定已選取並開啟 WAF 記錄檔。 它會在偵測模式中操作時，web 應用程式防火牆不會封鎖連入要求。
* **防止模式**:區塊入侵和攻擊偵測規則。 攻擊者會收到 「 403 未經授權的存取 」 例外狀況，並終止連接。 防止模式會在 WAF 記錄檔中記錄這類攻擊。

### <a name="anomaly-scoring-mode"></a>異常 Scoring 模式

OWASP 有兩種模式決定是否要封鎖的流量：傳統模式和異常分數的模式。

在傳統模式中，符合任何規則的流量會被視為獨立於任何其他規則相符項目。 此模式非常簡單易懂。 但缺乏幾個規則符合特定要求的資訊是一項限制。 因此，已引進異常評分模式。 它是 OWASP 3 的預設值。*x*。

在異常計分模式中，符合任何規則的流量不立即封鎖防火牆在防止模式時。 規則具有特定嚴重性：*關鍵*，*錯誤*，*警告*，或*通知*。 該嚴重性會影響的要求，稱為異常分數的數值。 例如，一個*警告*相符項目提供分數 3 的規則。 一*重大*相符項目提供 5 的規則。

|Severity  |值  |
|---------|---------|
|重要     |5|
|Error        |4|
|警告      |3|
|請注意       |2|

沒有為 5 來阻擋流量異常分數的臨界值。 因此，單一*重大*規則相符項目已足夠來封鎖的要求，甚至在防止模式中的應用程式閘道 WAF。 保留一個*警告*相符項目只會增加異常分數 3，這還不夠本身，以封鎖流量的規則。

> [!NOTE]
> WAF 規則符合流量時，會記錄此訊息包含的動作值 「 已封鎖。 」 但流量確實只封鎖的異常分數為 5 或更高版本。  

### <a name="waf-monitoring"></a>WAF 監視

監視您應用程式閘道的健康狀態非常重要。 Azure 監視器記錄和監視 WAF 和它所保護之應用程式的健全狀況與 Azure 資訊安全中心，Azure 監視器整合支援。

![應用程式閘道 WAF 診斷的圖表](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure 監視器

應用程式閘道記錄檔整合在一起[Azure 監視器](../monitoring-and-diagnostics/monitoring-overview.md)。 這可讓您追蹤包括 WAF 警示和記錄的診斷資訊。 您可以存取這項功能在**診斷**應用程式閘道資源，在入口網站，或直接透過 Azure 監視器中的索引標籤。 若要深入了解啟用記錄，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md)。

#### <a name="azure-security-center"></a>Azure 資訊安全中心

[資訊安全中心](../security-center/security-center-intro.md)可協助您防止、 偵測及回應威脅。 它提供進一步地掌握及控制您的 Azure 資源的安全性。 應用程式閘道[與資訊安全中心整合](application-gateway-integration-security-center.md)。 資訊安全中心會掃描您的環境，以偵測未受保護的 web 應用程式。 它可以建議應用程式閘道 WAF 保護這些易受攻擊的資源。 您可以建立防火牆直接從資訊安全中心。 這些 WAF 執行個體會與資訊安全中心整合。 這些報告，傳送警示和健康情況資訊到資訊安全中心。

![資訊安全中心概觀視窗](./media/waf-overview/figure1.png)

#### <a name="logging"></a>記錄

應用程式閘道 WAF 會提供詳細的報告，每個偵測到的威脅。 記錄會與 Azure 診斷記錄檔整合。 警示會記錄在.json 格式。 這些記錄可以與 [Azure 監視器記錄](../azure-monitor/insights/azure-networking-analytics.md)整合。

![應用程式閘道診斷記錄的 windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>應用程式閘道 WAF SKU 價格

應用程式閘道 WAF 會位於新的 SKU。 只能在 Azure Resource Manager 佈建模型中，在傳統部署模型中使用此 SKU。 此外，WAF SKU 提供只中型和大型應用程式閘道執行個體大小。 應用程式閘道的所有限制也適都用於 WAF SKU。

定價根據每小時的閘道執行個體費用和資料處理費用。 [應用程式閘道定價](https://azure.microsoft.com/pricing/details/application-gateway/)WAF SKU 與不同於標準 SKU 費用。 資料處理費用都相同。 沒有任何每一個規則群組的費用。 您可以保護相同的 web 應用程式防火牆後方的多個 web 應用程式。 您不需支付費用，以支援多個應用程式。

## <a name="next-steps"></a>後續步驟

請參閱[如何在應用程式閘道上設定 web 應用程式防火牆](tutorial-restrict-web-traffic-powershell.md)。
