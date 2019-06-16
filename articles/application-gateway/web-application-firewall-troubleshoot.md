---
title: 疑難排解 Azure 應用程式閘道的 Web 應用程式防火牆
description: 這篇文章提供 Azure 應用程式閘道的疑難排解資訊的 Web 應用程式防火牆 (WAF)
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082439"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>疑難排解 Azure 應用程式閘道的 Web 應用程式防火牆 (WAF)

有幾件事，如果應該傳遞透過您的 Web 應用程式防火牆 (WAF) 的要求都會遭到封鎖，您可以執行。

首先，請確定您已閱讀[WAF 概觀](waf-overview.md)並[WAF 設定](application-gateway-waf-configuration.md)文件。 此外，請確定您已啟用[WAF 監視](application-gateway-diagnostics.md)這些文章說明 WAF 的功能，WAF 規則如何設定工作，以及如何存取 WAF 記錄。

## <a name="understanding-waf-logs"></a>了解 WAF 記錄檔

WAF 記錄檔的目的是顯示每個要求會比對或封鎖的 WAF。 它是所有的評估要求，會比對或封鎖的交易記錄資料庫。 如果您注意到，WAF 會封鎖要求，它不應該 （誤判），您可以執行一些動作。 首先，縮小，並尋找特定的要求。 請查看記錄檔，以尋找特定的 URI、 時間戳記或交易識別碼的要求。 當您找到相關聯的記錄項目時，您可以開始處理的誤判。

例如，假設您有合法的流量，其中包含的字串*1 = 1*您想要通過 WAF。 如果您嘗試在要求時，WAF 會封鎖流量，其中包含您*1 = 1*中任何參數或欄位的字串。 這是通常與 SQL 資料隱碼攻擊相關聯的字串。 您可以查看記錄檔，並查看要求及封鎖/比對規則的時間戳記。

在下列範例中，您可以看到四個規則會觸發在相同的要求 （使用 TransactionId 欄位）。 第一個說它符合，因為使用者使用的數字/IP URL 要求，進而增加三個異常分數，因為它是一個警告。 比對的下一個規則是 942130，這是您要尋找的一個。 您所見*1 = 1*在`details.data`欄位。 這進一步增加異常分數的三個同樣地，因為它也是警告。 一般而言，每個規則具有動作**Matched**增加異常分數，並在此時異常分數就是六個。 如需詳細資訊，請參閱 <<c0> [ 異常評分模式](waf-overview.md#anomaly-scoring-mode)。

最終的兩個記錄項目會顯示要求遭到封鎖，因為夠高的異常分數。 這些項目具有比其他兩個不同的動作。 它們實際上顯示他們*封鎖*要求。 這些規則是必要項目，而且無法停用。 它們不應該視為規則，而更 WAF 內部運作的核心基礎結構。

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>修正誤判

這項資訊，與規則 942130 是比對的知識*1 = 1*字串，您可以從封鎖您的流量停止這幾件事：

- 使用排除清單

   請參閱[WAF 設定](application-gateway-waf-configuration.md#waf-exclusion-lists)如需有關排除清單。
- 停用規則。

### <a name="using-an-exclusion-list"></a>使用排除清單

若要進行明智的決策處理 false 正值，請務必熟悉應用程式所使用的技術。 例如，假設您的技術堆疊，在沒有 SQL server，但卻收到這些規則的相關的誤判。 停用這些規則並不一定會減弱您的安全性。

使用排除清單的其中一個優點是要求的，已停用特定組件。 不過，這表示特定排除不適用於所有的流量通過 WAF，因為它是一種全域設定。 比方說，這可能會導致問題如果*1 = 1*內的特定應用程式，但不適用於其他有效的要求。 另一個優點是，您可以選擇主體、 標頭和 cookie 要排除符合特定條件時，相對於排除整個要求。

有時候，有一些的情況其中特定參數取得傳遞至可能不是直覺式的方式中的 WAF。 比方說，是使用 Azure Active Directory 進行驗證時傳遞的權杖。 此語彙基元，*為 __RequestVerificationToken*，通常是取得傳入要求的 cookie。 不過，在某些情況下，會在停用 cookie，這個語彙基元也會傳遞做為要求的屬性或 「 引數 」。 如果發生這種情況，您必須確定*為 __RequestVerificationToken*新增至排除清單**要求屬性名稱**以及。

![排除](media/waf-tshoot/exclusion-list.png)

在此範例中，您想要排除**要求的屬性名稱**，equals *text1*。 這是很明顯，因為您可以看到防火牆記錄檔中的屬性名稱：**資料：相符的資料：1 = 1 ARGS:text1 中找到：1=1**. 此屬性是**text1**。 您也可以找到此屬性名稱其他幾種方式，請參閱[尋找要求的屬性名稱](#finding-request-attribute-names)。

![WAF 排除清單](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>停用規則

避開 false 正數的另一個方法是停用規則相符輸入 WAF 思考惡意。 因為您已剖析的 WAF 記錄檔，並已縮小到 942130 規則，您可以在 Azure 入口網站中停用它。 請參閱[自訂 web 應用程式防火牆規則，透過 Azure 入口網站](application-gateway-customize-waf-rules-portal.md)。

停用規則的其中一個優點是，如果您知道包含通常會被封鎖的特定條件的所有流量都是真實的流量，您可以停用該規則整個 waf。 不過，如果只是真實的流量，在特定使用案例中，您開放的弱點可能會對整個 WAF 停用該規則，因為它是一種全域設定。

如果您想要使用 Azure PowerShell，請參閱[自訂 web 應用程式防火牆規則，透過 PowerShell](application-gateway-customize-waf-rules-powershell.md)。 如果您想要使用 Azure CLI，請參閱[自訂 web 應用程式防火牆規則，透過 Azure CLI](application-gateway-customize-waf-rules-cli.md)。

![WAF 規則](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>尋找要求的屬性名稱

借助[Fiddler](https://www.telerik.com/fiddler)，您檢查個別要求，並判斷哪些特定欄位的網頁會呼叫。 這有助於排除檢查使用排除清單中的某些欄位。

在此範例中，您可以看到欄位所在*1 = 1*輸入的字串稱為**text1**。

![Fiddler](media/waf-tshoot/fiddler-1.png)

這是您可以排除欄位。 若要深入了解排除清單，請參閱[Web 應用程式防火牆的要求大小限制和排除清單](application-gateway-waf-configuration.md#waf-exclusion-lists)。 您可以藉由設定下列的排除在此情況下排除評估：

![WAF 排除](media/waf-tshoot/waf-exclusion-02.png)

您也可以檢查防火牆記錄檔，以取得的資訊，請參閱您要新增至排除清單。 若要啟用記錄，請參閱[後端健康情況、 診斷記錄和度量的應用程式閘道](application-gateway-diagnostics.md)。

檢查防火牆記錄檔，並檢視針對您想要檢查的要求發生的時數的 PT1H.json 檔案。

在此範例中，您可以看到您有四個規則相同之 TransactionID，而且它們全都發生在完全相同的時間：

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

您了解如何的 CRS 規則設定的工作，和 CRS 規則集 3.0 搭配評分系統的異常狀況 (請參閱[Azure 應用程式閘道 Web 應用程式防火牆](waf-overview.md)) 您知道下面兩個規則與**動作：封鎖**封鎖屬性會根據總異常分數。 將焦點放在規則是兩個頂端。

因為使用者用來瀏覽至應用程式閘道，可以在此情況下忽略的數字的 IP 位址，則會記錄第一個項目。

第二個 （規則 942130） 是最有趣。 它比對的模式 (1 = 1)，和名為欄位，您可以看到，詳細資料**text1**。 遵循相同的上一個步驟，以排除**要求的屬性名稱**可**等於** **1 = 1**。

## <a name="finding-request-header-names"></a>尋找要求標頭名稱

Fiddler 是一個有用的工具，同樣地，來尋找要求標頭名稱。 在下列螢幕擷取畫面中，您可以看到這個的 GET 要求，其中包含的標頭*Content-type*， *User-agent*，依此類推。

![Fiddler](media/waf-tshoot/fiddler-2.png)

若要檢視要求和回應標頭的另一個方法是 chrome 的查看開發人員工具。 您可以按 F12 或按一下滑鼠右鍵->**檢查** -> **開發人員工具**，然後選取**網路** 索引標籤。載入網頁，然後按一下您想要檢查的要求。

![Chrome F12](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>尋找要求的 cookie 名稱

如果要求包含 cookie **Cookie**  索引標籤上可以選取要在 Fiddler 中檢視它們。

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>限制要消除誤判的全域參數

- 停用要求主體檢查

   藉由設定**檢查要求主體**為 off，要求主體的所有流量將不會評估您的 waf。 這可能是很有用，如果您知道要求主體不是對您的應用程式。

   停用此選項時，不會檢查要求主體。 標頭和 cookie 維持檢查，除非個別項目會排除使用排除清單功能。

- 檔案大小限制

   透過 waf 限制檔案大小，您要限制的狀況發生在您的 web 伺服器的攻擊的可能性。 允許上傳大型檔案，會增加您的後端耗用的風險。 限制檔案大小，以您的應用程式的一般使用案例是只是另一種方式防止攻擊。

   > [!NOTE]
   > 如果您知道您的應用程式永遠不需要任何檔案上傳超過指定的大小，您可以限制的設定限制的。

## <a name="next-steps"></a>後續步驟

請參閱[如何在應用程式閘道上設定 web 應用程式防火牆](tutorial-restrict-web-traffic-powershell.md)。
