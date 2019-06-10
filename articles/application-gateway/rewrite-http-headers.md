---
title: 請重寫與 Azure 應用程式閘道的 HTTP 標頭 |Microsoft Docs
description: 這篇文章提供重寫 HTTP 標頭，在 Azure 應用程式閘道的概觀
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 9160d300270bf1ab5043bee632d27bcc4b7bf332
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476028"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>請重寫應用程式閘道的 HTTP 標頭

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 標頭可讓用戶端和伺服器，將與要求或回應的其他資訊。 藉由重寫這些標頭，您可以完成重要工作，例如新增安全性相關的標頭欄位，例如 HSTS / X XSS 防護、 移除回應標頭欄位，可能會揭露機密資訊，以及移除從連接埠資訊X 轉送的標頭。

應用程式閘道可讓您新增、 移除或更新 HTTP 要求和回應標頭時要求和回應封包將用戶端與後端集區之間移動。 它可讓您新增條件，以確保只有在符合特定條件時，才指定的標頭會重寫。

應用程式閘道也支援數個[伺服器變數](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables)儲存要求和回應的其他資訊可幫助。 這可讓您更輕鬆地建立強大的重寫規則。

> [!NOTE]
>
> HTTP 標頭重寫支援僅適用於[Standard_V2 和 WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md)。

![重寫的標頭](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>支援的標頭

您可以重寫要求和回應，除了主機、 連接和升級的標頭中的所有標頭。 您也可以使用應用程式閘道建立自訂標頭，並將它們新增至的要求和回應透過它路由傳送。

## <a name="rewrite-conditions"></a>重寫條件

您可以使用重寫條件評估的 HTTP (S) 要求和回應內容，並執行標頭重寫僅當一或多個條件成立。 應用程式閘道會使用這些類型的變數，要評估的 HTTP (S) 要求和回應內容：

- 在要求中 HTTP 標頭。
- 在回應中的 HTTP 標頭。
- 應用程式閘道伺服器變數。

您可以使用條件來評估指定的變數是否存在，，是否指定的變數符合特定值，或是否指定的變數符合特定模式。 您使用[Perl 相容規則運算式 (PCRE) 程式庫](https://www.pcre.org/)設定規則運算式模式比對的條件。 若要深入了解規則運算式語法，請參閱[Perl 規則運算式中的主頁面](https://perldoc.perl.org/perlre.html)。

## <a name="rewrite-actions"></a>重寫動作

您可以使用重寫動作以指定您想要重寫的要求和回應標頭和標頭的新值。 您可以建立新的標頭、 修改現有的標頭的值或刪除現有的標頭。 新的標頭或現有的標頭的值可以設定這些類型的值：

- 文字。
- 要求標頭。 若要指定要求標頭，您需要使用語法 {http_req_*headerName*}。
- 回應標頭。 若要指定回應標頭，您需要使用語法 {http_resp_*headerName*}。
- 伺服器變數。 若要指定伺服器變數，您需要使用語法 {var_*serverVariable*}。
- 文字、 要求標頭、 回應標頭和伺服器變數的組合。

## <a name="server-variables"></a>伺服器變數

應用程式閘道會使用伺服器變數來儲存連接的伺服器的相關實用資訊，與用戶端和目前要求的連接。 儲存資訊的範例包括用戶端的 IP 位址和 web 瀏覽器類型。 伺服器變數會動態變更，例如，當載入新的頁面或表單張貼。 您可以使用這些變數來評估重寫條件，以及重寫標頭。

應用程式閘道支援這些伺服器變數：

| 變數名稱 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | 使用 X 轉送的用戶端要求標頭欄位`client_ip`變數 （請參閱本表中的稍後說明） 附加於格式 IP1，IP2、 lt;ip3，等等。 如果 X 轉送的欄位不是在用戶端要求標頭`add_x_forwarded_for_proxy`變數等於`$client_ip`變數。 當您想要重寫應用程式閘道所設定，以便標頭包含只有不含連接埠資訊的 IP 位址 X 轉送的標頭，此變數會特別有用。 |
| ciphers_supported          | 一份支援的用戶端的密碼。          |
| ciphers_used               | 已建立的 SSL 連接所使用的破解密碼的字串。 |
| client_ip                  | 從其應用程式閘道收到要求的用戶端 IP 位址。 如果沒有應用程式閘道和原始的用戶端之前, 的反向 proxy *client_ip*將傳回的 IP 位址的反向 proxy。 |
| client_port                | 用戶端的連接埠。                                                  |
| client_tcp_rtt             | TCP 連線的用戶端資訊。 在支援 TCP_INFO 通訊端選項的系統上使用。 |
| client_user                | 使用 HTTP 驗證時，會提供使用者名稱進行驗證。 |
| host                       | 依照此優先順序取用： 要求行的主機名稱、 主機要求標頭欄位中的主機名稱或比對要求的伺服器名稱。 |
| cookie_*name*              | *名稱*cookie。                                            |
| http_method                | 用來進行處理 URL 要求的方法。 例如，GET 或 POST。 |
| http_status                | 工作階段狀態。 例如，200、 400 或 403。                       |
| http_version               | 要求的通訊協定。 通常 HTTP/1.0、 HTTP/1.1 或 HTTP/2.0。 |
| query_string               | 下列變數/值組的清單"？"中要求的 URL。 |
| received_bytes             | 要求 （包括要求行、 標頭和要求本文） 的長度。 |
| request_query              | 在要求行引數。                                |
| request_scheme             | 要求配置： http 或 https。                            |
| request_uri                | 完整的原始要求 URI （以引數）。                   |
| sent_bytes                 | 用戶端傳送的位元組數目。                             |
| server_port                | 已接受要求的伺服器連接埠。                 |
| ssl_connection_protocol    | 已建立的 SSL 連線的通訊協定。        |
| ssl_enabled                | 「 開啟 」 如果連線以 SSL 模式下運作。 否則為空字串。 |

## <a name="rewrite-configuration"></a>重寫組態

若要設定 HTTP 標頭重寫，您需要完成下列步驟。

1. 建立物件所需的 HTTP 標頭重寫：

   - **請重寫動作**:用來指定要求和您想要重寫的要求標頭欄位和標頭的新值。 您可以將其中一個或多個重寫重寫動作的情況。

   - **請重寫條件**:選擇性的組態。 重寫條件評估結果的 HTTP (S) 要求和回應的內容。 如果在 HTTP (S) 要求或回應符合重寫條件，就會發生重新寫入動作。

     如果您將多個條件與動作時，只在符合所有條件時，就會發生的動作。 換句話說，此作業是邏輯 AND 運算。

   - **重寫規則**:包含多個重寫動作 / 重寫條件組合。

   - **規則順序**:可協助判斷在其中執行的重寫規則的順序。 當您在重寫組有多個重寫規則，此設定是很有幫助。 重寫規則具有較低的規則順序值會先執行。 如果您將相同的規則順序指派給兩個重寫規則時，執行順序是不具決定性。

   - **請重寫組**:包含與要求的路由規則相關聯的多個重寫規則。

2. 附加重新寫入集合 (*rewriteRuleSet*) 路由的規則。 重寫組態會附加至來源接聽程式，透過路由規則。 當您使用基本路由規則時，標頭重寫組態與來源接聽程式相關聯，並是全域的標頭重寫。 當您使用路徑型路由規則時，標頭重寫組態被定義 URL 路徑對應。 在此情況下，它只適用於站台的特定路徑區域。

您可以建立多個 HTTP 標頭重寫集，並套用設定多個接聽程式為每個重寫。 但您可以只有一個重寫設為特定的接聽程式。

## <a name="common-scenarios"></a>常見案例

以下是一些常見的案例，使用標頭重寫。

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>移除 X 轉送的標頭中的連接埠資訊

它將要求轉送至後端之前，應用程式閘道會將 X 轉送的標頭插入所有要求。 此標頭是以逗號分隔清單的 IP 連接埠。 可能情況下後, 端伺服器只需要這些標頭將包含 IP 位址。 您可以使用標頭重寫，若要移除 X 轉送的標頭中的連接埠資訊。 若要這樣做的一個方式是將標頭設定 add_x_forwarded_for_proxy 伺服器變數：

![移除連接埠](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>修改重新導向 URL

當後端應用程式傳送的重新導向回應時，您可以在用戶端重新導向至與後端應用程式指定不同的 URL。 比方說，您可能要這樣做，當 app service 裝載的應用程式閘道後面，而且需要用戶端以執行重新導向至其相對的路徑。 （例如，從 contoso.azurewebsites.net/path1 重新導向至 contoso.azurewebsites.net/path2。）

因為 App Service 是多租用戶的服務，它會使用主機標頭中要求將要求路由至正確的端點。 應用程式服務有預設的網域名稱的 *。 azurewebsites.net (例如 contoso.azurewebsites.net) 不同的應用程式閘道的網域名稱 (例如 contoso.com)。 因為原始要求從用戶端與主機名稱的應用程式閘道的網域名稱 (contoso.com)，應用程式閘道會變成 contoso.azurewebsites.net 的主機名稱。 它會讓這項變更，讓 app service 可以將要求路由至正確的端點。

當應用程式服務會傳送重新導向回應時，它會使用相同的主機名稱在其回應的 location 標頭與從應用程式閘道收到要求。 因此用戶端將要求直接 contoso.azurewebsites.net/path2 而不會搜查應用程式閘道 (contoso.com/path2)。 略過應用程式閘道並不恰當。

您可以藉由設定應用程式閘道的網域名稱位置標頭中的主機名稱來解決此問題。

以下是取代主機名稱的步驟：

1. 如果在回應中的 location 標頭包含 azurewebsites.net 評估的條件建立重寫規則。 輸入模式`(https?):\/\/.*azurewebsites\.net(.*)$`。
1. 執行動作，來重寫的 location 標頭，使其具有應用程式閘道的主機名稱。 進入`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`做為標頭值。

![修改位置標頭](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>實作 HTTP 安全性標頭，以防產生漏洞

您可以藉由實作必要的標頭中的應用程式回應修正數個安全性弱點。 這些安全性標頭包含 X XSS 防護、 Strict-傳輸層安全性和內容安全性原則。 您可以使用應用程式閘道設定適用於所有回應這些標頭。

![安全性標頭](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>刪除不必要的標頭

您可能想要移除顯示之 HTTP 回應中的機密資訊的標頭。 例如，您可能想要移除後端伺服器名稱、 作業系統或媒體櫃詳細資料等資訊。 若要移除這些標頭，您可以使用應用程式閘道：

![刪除標頭](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>檢查標頭存在

您可以評估標頭或伺服器變數的目前狀態的 HTTP 要求或回應標頭。 當您想要在特定的標頭已存在時，才執行標頭重寫，這項評估是很有用。

![檢查標頭的存在](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>限制

- 如果回應有一個以上具有相同名稱的標頭，然後重寫其中一個這些標頭的值會導致在回應中卸除其他標頭。 這通常可以進行 Set-cookie 標頭，因為您可以在回應中的多個 Set-cookie 標頭。 這類案例之一是當您使用 app service 應用程式閘道，並已在應用程式閘道上設定 cookie 型工作階段親和性。 在此情況下回應會包含 2 個 Set-cookie 標頭： 其中一個服務使用的應用程式，亦即`Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net`，而另一個應用程式閘道親和性，亦即， `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`。 重寫其中一個 Set-cookie 標頭，在此案例中可能會導致從回應中移除其他 Set-cookie 標頭。

- 重寫的連線、 升級和主機標頭目前不支援。

- 標頭名稱可以包含任何英數字元和特定符號，如同[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27)。 我們目前不支援底線 (\_) 標頭名稱中的特殊字元。

## <a name="next-steps"></a>後續步驟

若要了解如何重新撰寫 HTTP 標頭，請參閱：

- [重新撰寫使用 Azure 入口網站的 HTTP 標頭](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [重新撰寫使用 Azure PowerShell 的 HTTP 標頭](add-http-header-rewrite-rule-powershell.md)
