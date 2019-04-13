---
title: 重寫 Azure 應用程式閘道中的 HTTP 標頭 | Microsoft Docs
description: 本文概要說明重寫「Azure 應用程式閘道」中 HTTP 標頭的功能
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: efb7b46919066beb1382d70b676a2115ea0fb8ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544138"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>使用應用程式閘道來重寫 HTTP 標頭 (公開預覽)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 標頭允許用戶端和伺服器透過要求或回應傳遞其他資訊。 重寫這些 HTTP 標頭可協助您完成數個重要的案例，例如新增安全性相關的標頭欄位，例如 HSTS/X XSS 保護，移除回應標頭欄位的可能會揭露機密資訊，從清除連接埠資訊X 轉送的標頭等等。應用程式閘道支援的功能可新增、 移除或更新 HTTP 要求和回應標頭時要求和回應封包用戶端與後端集區之間移動。 它也提供讓您將新增條件，以確保只有在符合特定條件時，才指定的標頭會重寫。
> [!NOTE]
>
> HTTP 標頭重寫支援僅適用於 [新 SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

## <a name="headers-supported-for-rewrite"></a>請重寫支援的標頭

此功能可讓您重寫要求與回應皆主機、 連接和升級的標頭中的所有標頭。 您也可以使用應用程式閘道建立自訂標頭，並將其新增為要求和回應透過它路由傳送。 

## <a name="rewrite-conditions"></a>重寫條件

使用只在一個或多個條件符合時，請重寫情況，您可以評估內容的 HTTP (S) 要求和回應，並執行標頭重寫。 應用程式閘道會使用下列 3 種變數評估 HTTP (S) 要求和回應的內容：

- 在要求中的 HTTP 標頭
- 在回應中的 HTTP 標頭
- 應用程式閘道伺服器變數

可用來評估指定的變數是否存在，，或是否完全符合指定的變數的特定值，指定的變數會完全符合特定模式是否設定了條件。 [Perl 相容規則運算式 (PCRE) 程式庫](https://www.pcre.org/)用來實作規則運算式模式比對的條件。 若要深入了解的規則運算式語法，請參閱[Perl 規則運算式 man 頁面](http://perldoc.perl.org/perlre.html)。

## <a name="rewrite-actions"></a>重寫動作

請重寫動作用來指定您想要重寫的要求和回應標頭和原始的標頭需要重寫，以新值。 您可以建立新的標頭、 修改現有的標頭的值中，或刪除現有的標頭中。 新的標頭或現有的標頭的值可以設定為下列類型的值：

- 文字 
- 要求標頭：若要指定要求標頭，您必須使用語法 {http_req_*headerName*}
- 回應標頭：若要指定回應標頭，您必須使用語法 {http_resp_*headerName*}
- 伺服器變數：若要指定伺服器變數，您必須使用語法 {var_*serverVariable*}
- 文字、 要求標頭、 回應標頭和伺服器變數的組合。

## <a name="server-variables"></a>伺服器變數

應用程式閘道會使用伺服器變數來儲存連接，例如用戶端的 IP 位址或 web 瀏覽器類型上的實用資訊的伺服器與用戶端和目前要求的連接。 這些變數，以動態方式變更例如新的頁面載入時或發佈表單。 您可以使用這些伺服器變數來評估重寫條件，以及重寫標頭。 

應用程式閘道支援下列伺服器變數：

| 支援的伺服器變數 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | 包含 「 X-轉送-若為 「 用戶端要求標頭欄位與`client_ip`（在此表中說明） 附加至它的格式 (IP1，IP2，lt;ip3，...) 的變數。如果 「 X-轉送-若為"欄位不存在於用戶端要求標頭中，`add_x_forwarded_for_proxy`變數等於`$client_ip`變數。 此變數是在客戶想要重寫應用程式閘道所設定的 X 轉送的標頭，標頭包含只有不含連接埠資訊的 IP 位址的案例中特別有用。 |
| ciphers_supported          | 會傳回用戶端所支援的加密方式          |
| ciphers_used               | 會傳回用於所建立 SSL 連線的加密方式字串 |
| client_ip                  | 從其應用程式閘道收到要求的用戶端的 IP 位址。 如果沒有應用程式閘道和原始的用戶端之前, 的反向 proxy 的再*client_ip*將傳回的 IP 位址的反向 proxy。 |
| client_port                | 用戶端連接埠                                                  |
| client_tcp_rtt             | 有關用戶端 TCP 連線的資訊；是支援 TCP_INFO 通訊端選項之系統上的可用變數 |
| client_user                | 使用 HTTP 驗證時，為驗證提供的使用者名稱 |
| host                       | 優先順序如下：來自要求行的主機名稱或來自 “Host” 要求標頭欄位的主機名稱，或是與要求相符的伺服器名稱 |
| cookie_*name*              | *名稱*cookie                                            |
| http_method                | 用來提出 URL 要求的方法。 例如 GET、POST 等。 |
| http_status                | 工作階段狀態，例如：200、400、403 等。                       |
| http_version               | 要求通訊協定，通常是 “HTTP/1.0”、“HTTP/1.1” 或 “HTTP/2.0” |
| query_string               | 接在所要求 URL 中 "?" 後面的「變數-值」組清單。 |
| received_bytes             | 要求長度 (包括要求行、標頭及要求本文) |
| request_query              | 要求行中的引數                                |
| request_scheme             | 要求配置 (“http” 或 “https”)                            |
| request_uri                | 完整的原始要求 URI (含引數)                   |
| sent_bytes                 | 傳送給用戶端的位元組數                             |
| server_port                | 接受要求之伺服器的連接埠                 |
| ssl_connection_protocol    | 會傳回所建立 SSL 連線的通訊協定        |
| ssl_enabled                | 連線以 SSL 模式運作時為 “on”，使用其他模式時則為空字串 |

## <a name="rewrite-configuration"></a>重寫組態

若要設定 HTTP 標頭重寫，您將需要：

1. 建立重寫 HTTP 標頭所需的新物件：

   - **請重寫動作**： 用來指定要求和您想要重寫的要求標頭欄位和原始的標頭需要重寫，以新值。 您可以選擇將關聯一或多個重寫條件重寫動作。

   - **請重寫條件**:它是選擇性的設定。 如果新增重寫條件，則會評估的 HTTP (S) 要求和回應的內容。 是否與重寫條件相符的 HTTP (S) 要求或回應以決定来執行的重寫條件相關聯的重寫動作。 

     如果多個條件相關聯動作，則動作將會執行只有當符合所有條件，也就是，將執行邏輯 AND 運算。

   - **重寫規則**： 重寫規則包含多個重寫動作-重寫條件組合。

   - **規則順序**： 能協助您判斷執行所在的不同重寫規則的順序。 重寫集合中有多個重寫規則時，這是很有幫助。 取得第一次執行的重寫規則，以較低的規則順序值。 如果您提供兩個重寫規則的相同規則順序執行的順序將會不具決定性的。

   - **請重寫組**： 包含多個重寫規則的要求路由規則建立關聯。

2. 您必須將連接重新撰寫組 (*rewriteRuleSet*) 與路由規則。 這是因為的重寫組態附加至來源接聽程式，透過路由規則。 使用基本路由規則時，標頭重寫設定會與來源接聽程式相關聯，並且會是全域標頭重寫。 使用路徑型錄由規則時，會在 URL 路徑對應上定義標頭重寫設定。 因此，它只適用於網站的特定路徑區域。

您可以建立多個 http 標頭重寫集，而且每個重寫集可以套用至多個接聽程式。 不過，您可以套用只有一個重寫設為特定的接聽程式。

## <a name="common-scenarios"></a>常見案例

下面所述的一些常見案例需要標頭重寫。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>移除 X 轉送的標頭中的連接埠資訊

它將要求轉送至後端之前，應用程式閘道會插入 X-轉送標頭的所有要求。 此標頭的格式是以逗號分隔的 ip: port 清單。 不過，可能是其中的後端伺服器要求標頭，使其僅包含 IP 位址的案例。 完成這種情況下，標頭重寫可用來移除 X 轉送的標頭中的連接埠資訊。 若要這樣做的一個方式是將標頭設定為 add_x_forwarded_for_proxy 伺服器變數。 

![移除連接埠](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>修改重新導向 URL

當後端應用程式傳送的重新導向回應時，您可能想要重新導向至不同的 URL，由後端應用程式所指定的用戶端。 這類案例之一是 app service 裝載的應用程式閘道後面，而且需要執行重新導向至其相對路徑 （重新導向從 contoso.azurewebsites.net/path1 contoso.azurewebsites.net/path2） 用戶端。 

由於應用程式服務是多租用戶服務，它會使用主機標頭中要求路由傳送至正確的端點。 應用程式服務有預設的網域名稱的 *。 azurewebsites.net (例如 contoso.azurewebsites.net) 的應用程式閘道的網域名稱 (例如 contoso.com) 不同。 由於原始要求從用戶端應用程式閘道的名稱 contoso.com 網域的主機名稱，應用程式閘道變更主機名稱為 contoso.azurewebsites.net，使應用程式服務可以將它路由至正確的端點。 當應用程式服務會傳送重新導向回應時，它會使用相同的主機名稱在其回應的 location 標頭與從應用程式閘道收到要求。 因此，用戶端將要求直接 contoso.azurewebsites.net/path2，而不會搜查應用程式閘道 (contoso.com/path2)。 略過應用程式閘道不需要這樣做。 

藉由設定應用程式閘道的網域名稱位置標頭中的主機名稱，就可以解決此問題。 若要這樣做，您可以建立重寫規則的條件來評估，如果在回應中的 location 標頭包含 azurewebsites.net 輸入`(https?):\/\/.*azurewebsites\.net(.*)$`作為模式，並執行動作，來重寫至有應用程式閘道的位置標頭輸入主機名稱`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`做為標頭值。

![修改位置標頭](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>實作 HTTP 安全性標頭，以防產生漏洞

可以藉由實作必要的標頭中的應用程式回應修正數個安全性弱點。 這些安全性標頭有些 X XSS 防護、 Strict 傳輸安全性、 內容安全性原則，等等。您可以使用應用程式閘道設定適用於所有回應這些標頭。

![安全性標頭](media/rewrite-http-headers/security-header.png)

## <a name="limitations"></a>限制

- 重寫的連線、 升級和主機標頭尚不支援。

- 標頭名稱可以包含任何英數字元和 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) 中所定義的特定符號。 不過，我們目前不支援在標頭名稱中使用「底線」(\_) 特殊字元。 

## <a name="need-help"></a>需要協助嗎？

如需有關此功能的任何協助，請透過 [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) 與我們連絡。

## <a name="next-steps"></a>後續步驟

若要了解如何重新撰寫 HTTP 標頭，請參閱：

-  [重新撰寫使用 Azure 入口網站的 HTTP 標頭](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [重新撰寫使用 Azure PowerShell 的 HTTP 標頭](add-http-header-rewrite-rule-powershell.md)