---
title: 重寫 Azure 應用程式閘道中的 HTTP 標頭 | Microsoft Docs
description: 本文概要說明重寫「Azure 應用程式閘道」中 HTTP 標頭的功能
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 67603e326583400e8fc250ea6120297e7a94d101
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520916"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>使用應用程式閘道來重寫 HTTP 標頭 (公開預覽)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

HTTP 標頭允許用戶端和伺服器透過要求或回應傳遞其他資訊。 重新撰寫這些 HTTP 標頭可幫助您完成數個重要的情況，例如新增與安全性相關的標頭欄位 (例如 HSTS/ X-XSS-Protection) 或移除可能會揭露機密資訊 (例如後端伺服器名稱) 的回應標頭欄位。

「應用程式閘道」現在支援重寫傳入 HTTP 要求標頭及傳出 HTTP 回應標頭的能力。 您將能夠在要求/回應封包於用戶端與後端集區之間移動時，新增、移除或更新 HTTP 要求和回應標頭。 您可以重寫標準標頭欄位，也可以重寫非標準標頭欄位。

> [!NOTE]
> 
> HTTP 標頭重寫支援僅適用於 [新 SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

「應用程式閘道」標頭重寫支援提供：

- **全域標頭重寫**：您可以重寫網站所有相關要求和回應的特定標頭。
- **路徑為基礎的標頭重寫**： 這種類型的重寫，可讓 「 標頭重寫要求和回應的相關只在特定站台 區域上的購物車區域，例如以 /cart/\*。

進行這項變更新時，您必須：

1. 建立重寫 HTTP 標頭所需的新物件： 
   - **RequestHeaderConfiguration**：此物件可用來指定您想要重寫的要求標頭欄位，以及原始標頭在重寫後必須採用的新值。
   - **ResponseHeaderConfiguration**：此物件可用來指定您想要重寫的回應標頭欄位，以及原始標頭在重寫後必須採用的新值。
   - **ActionSet**：此物件包含以上所指定要求和回應標頭的設定。 
   - **RewriteRule**：此物件包含以上所指定的所有 *actionSet*。 
   - **RewriteRuleSet**：此物件包含所有 *rewriteRule*，且將必須連結至要求路由規則 - 基本或路徑型。
2. 接著，您將必須藉由路由規則連結重寫規則集。 此重寫設定在建立之後，就會透過路由規則連結至來源接聽程式。 使用基本路由規則時，標頭重寫設定會與來源接聽程式相關聯，並且會是全域標頭重寫。 使用路徑型錄由規則時，會在 URL 路徑對應上定義標頭重寫設定。 因此，它只適用於網站的特定路徑區域。

您可以建立多個 HTTP 標頭重寫規則集，且每個重寫規則集都可套用至多個接聽程式。 不過，您只能將一個 HTTP 重寫規則集套用至一個特定接聽程式。

您可以將標頭中的值重寫成：

- 文字值。 

  *範例：* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- 來自另一個標頭的值。 

  *範例 1：* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > 若要指定要求標頭，您必須使用此語法：{http_req_headerName}

  *範例 2：*

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > 若要指定回應標頭，您必須使用此語法：{http_resp_headerName}

- 來自所支援伺服器變數的值。

  *範例：* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > 若要指定伺服器變數，您必須使用此語法：{var_serverVariable}

- 上述幾個值的組合。

## <a name="server-variables"></a>伺服器變數

伺服器變數會在 Web 伺服器上儲存有用的資訊。 這些變數提供與下列各項相關的資訊：伺服器、與用戶端的連線，以及目前連線上的要求，例如用戶端 IP 位址或用戶端 IP 位址或 Web 瀏覽器類型。 它們會以動態方式變更，例如載入新頁面時或發佈表單時。  使用者可以使用這些變數以設定要求標頭以及回應標頭。 

此功能支援將標頭重寫成下列伺服器變數：

| 支援的伺服器變數 | 描述                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | 會傳回用戶端所支援的加密方式          |
| ciphers_used               | 會傳回用於所建立 SSL 連線的加密方式字串 |
| client_ip                  | 從其應用程式閘道收到要求的用戶端的 IP 位址。 如果沒有應用程式閘道和原始的用戶端之前, 的反向 proxy 的再*client_ip*會傳回反向 proxy 的 IP 位址。 tjsi 變數是在客戶想要重寫應用程式閘道所設定的 X 轉送的標頭，以便標頭包含只有不含連接埠資訊的 IP 位址的案例中特別有用。 |
| client_port                | 用戶端連接埠                                                  |
| client_tcp_rtt             | 有關用戶端 TCP 連線的資訊；是支援 TCP_INFO 通訊端選項之系統上的可用變數 |
| client_user                | 使用 HTTP 驗證時，為驗證提供的使用者名稱 |
| host                       | 優先順序如下：來自要求行的主機名稱或來自 “Host” 要求標頭欄位的主機名稱，或是與要求相符的伺服器名稱 |
| cookie_*name*              | *名稱*cookie |
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

## <a name="limitations"></a>限制

- 目前只有透過 Azure PowerShell、Azure API 及 Azure SDK，才能使用這項重寫 HTTP 標頭的功能。 近期將會透過入口網站和 Azure CLI 支援此功能。

- 只有在新 SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 上才支援 HTTP 標頭重寫支援。 在舊 SKU 上將不支援此功能。

- 重寫的連線、 升級和主機標頭尚不支援。

- 近期將會推出可依條件重寫 HTTP 標頭的功能。

- 標頭名稱可以包含任何英數字元和 [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) 中所定義的特定符號。 不過，我們目前不支援在標頭名稱中使用「底線」(\_) 特殊字元。 

## <a name="need-help"></a>需要協助嗎？

如需有關此功能的任何協助，請透過 [AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) 與我們連絡。

## <a name="next-steps"></a>後續步驟

在了解重寫 HTTP 標頭的功能之後，請移至[建立會重寫 HTTP 標頭的自動調整規模和區域備援應用程式閘道](tutorial-http-header-rewrite-powershell.md) \(英文\) 或[重寫現有自動調整規模和區域備援應用程式閘道中的 HTTP 標頭](add-http-header-rewrite-rule-powershell.md) \(英文\)
