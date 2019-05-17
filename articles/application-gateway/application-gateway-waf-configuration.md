---
title: Azure 應用程式閘道中的 Web 應用程式防火牆要求大小限制與排除清單 - Azure 入口網站
description: 此文章說明如何使用 Azure 入口網站來設定「應用程式閘道」中的 Web 應用程式防火牆要求大小限制與排除清單設定。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 5ddcdeca41e2f21fa27db25f7e0721c7ef87e491
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620285"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Web 應用程式防火牆要求大小限制與排除清單

Azure 應用程式閘道 Web 應用程式防火牆 (WAF) 提供 Web 應用程式的保護。 此文章說明 WAF 要求大小限制與排除清單設定。

## <a name="waf-request-size-limits"></a>WAF 要求大小限制

![要求大小限制](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Web 應用程式可讓您設定介於上下限範圍之間的要求大小限制。 下列是兩個可用的大小限制：

- Kb 和控制整體的要求大小限制，但不包括任何檔案上傳中未指定最大要求主體大小 欄位。 此欄位值的範圍可從最小 1 KB 到最大 128 KB。 要求本文大小的預設值為 128 KB。
- 檔案上傳限制欄位是以 MB 為單位指定，而且它會控管允許的檔案上傳大小上限。 當中型 SKU 的最大值為 100 MB 時，大型 SKU 執行個體的這個欄位其最小值為 1MB，最大值為 500 MB。 檔案上傳限制的預設值為 100 MB。

WAF 也可提供可設定的旋鈕，以便開啟或關閉要求本文檢查。 根據預設，要求本文檢查是啟用的。 如果要求主體檢查已關閉時，WAF 不評估 HTTP 訊息本文的內容。 在此情況下，WAF 會繼續針對標頭、Cookie 與 URI 強制執行 WAF 規則。 如果要求本文檢查關閉，則最大要求本文大小欄位就不適用，而且也無法設定。 關閉要求本文檢查可讓要傳送給 WAF 的訊息大於 128 KB，但不會檢查訊息本文是否有漏洞。

## <a name="waf-exclusion-lists"></a>WAF 排除清單

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF 排除清單可讓您略過 WAF 評估的特定要求屬性。 常見範例是用於驗證或密碼欄位的 Active Directory 插入式權杖。 這類屬性較可能包含特殊字元，而會觸發 WAF 規則的誤判。 一旦屬性新增至 WAF 排除清單，任何已設定和作用中 WAF 規則就不會考慮該屬性。 排除清單的範圍是全域的。

下列屬性可以新增至排除清單：

* 要求標頭
* 要求 Cookie
* 要求的屬性名稱 (argumenty)

   * 表單多部分資料
   * XML
   * JSON
   * URL 查詢引數

您可以指定要精確比對要求標頭、本文、Cookie 或查詢字串屬性。  或者，您也可以選擇指定部分相符即可。 排除規則只會用於標頭欄位，一律不會在其值上使用。 排除規則屬於全域範圍，可套用至所有頁面和所有規則。

以下是支援的比對條件運算子：

- **等於**：此運算子適用於精確比對。 例如，若要選取名為 **bearerToken** 的標頭，請使用等於運算子搭配設定為 **bearerToken** 的選取器。
- **開頭為**：此運算子會比對開頭與指定之選取器值相符的所有欄位。
- **結尾為**：此運算子會比對結尾與指定之選取器值相符的所有欄位。
- **包含**：此運算子會比對包含與指定之選取器值相符的所有欄位。
- **等于任何值**：此运算符与所有请求字段匹配。 * 将是选择器值。

在所有情況下，比對都不會區分大小寫，且不允許使用規則運算式作為選取器。

### <a name="examples"></a>範例

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下列範例示範如何使用排除項目。

### <a name="example-1"></a>範例 1

在此範例中，您會想要排除的使用者代理標頭。 使用者代理程式的要求標頭包含特性的字串，可讓網路通訊協定對等，來識別應用程式類型、 作業系統、 軟體廠商或提出要求的軟體使用者代理程式軟體版本。 如需詳細資訊，請參閱 < [User-agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)。

可以有任意數目的原因，若要停用評估此標頭。 可能會看到 WAF，並假設它是惡意的字串。 例如，傳統的 SQL 攻擊"x = x 」 在字串中。 在某些情況下，這可以是合法的流量。 因此，您可能需要排除 WAF 評估此標頭。

下列 Azure PowerShell cmdlet 從評估中排除的使用者代理標頭：

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>範例 2

此範例會排除中的值*使用者*透過 URL 要求中傳遞的參數。 例如，假設您通常會在您環境中的 [使用者] 欄位必須包含 WAF 檢視做為惡意的內容，因此會封鎖它的字串。  在此情況下，如此 WAF 不評估任何項目欄位中，您可以排除 user 參數。

下列 Azure PowerShell cmdlet 從評估中排除使用者參數：

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
因此，如果 URL **http://www.contoso.com/?user=fdafdasfda**傳遞至 WAF，它將不會評估字串**fdafdasfda**。

## <a name="next-steps"></a>後續步驟

在您設定 WAF 設定之後，您可以了解如何檢視 WAF 記錄。 如需詳細資訊，請參閱[應用程式閘道診斷](application-gateway-diagnostics.md#diagnostic-logging)。
