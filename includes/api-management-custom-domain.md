## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>APIM Proxy 伺服器使用 SSL 憑證中的 TLS 信號交換的回應方式

### <a name="clients-calling-with-sni-header"></a>SNI 標頭，以呼叫的用戶端
如果客戶有針對 Proxy 設定的一或多個自訂的網域，APIM 可以回應 HTTPS 要求的自訂網域 (例如，contoso.com) 以及預設網域 (例如，apim 服務-name.azure-需將)。 根據伺服器名稱指示 (SNI) 標頭中的資訊，APIM 會回應適當的伺服器憑證。

### <a name="clients-calling-without-sni-header"></a>用戶端呼叫沒有 SNI 標頭
如果客戶使用的用戶端不會傳送[SNI](https://tools.ietf.org/html/rfc6066#section-3)標頭，APIM 建立回應根據下列邏輯：

* 如果服務有一個自訂網域的 Proxy 設定，預設的憑證是 Proxy 自訂網域已發出的憑證。
* 如果服務已設定多個自訂網域的 Proxy (中才支援**Premium**層)，客戶可以將指定的憑證應該是預設的憑證。 若要設定預設的憑證， [defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#definitions_hostnameconfiguration)屬性應該設定為 true (「 defaultSslBinding":"true")。 如果客戶未設定屬性，預設的憑證是發給 *.azure 需將在裝載的預設 Proxy 網域的憑證。

## <a name="support-for-putpost-request-with-large-payload"></a>支援大型裝載 PUT/POST 要求

APIM Proxy 伺服器支援大型裝載的要求，使用 https 的用戶端憑證時 (例如，裝載 > 40 KB)。 若要防止凍結伺服器的要求，客戶可以設定屬性["negotiateClientCertificate":"true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#definitions_hostnameconfiguration)上的 Proxy 主機名稱。 如果此屬性設定為 true，用戶端憑證要求之前的時間 SSL/TLS 連線，任何 HTTP 要求交換。 因為此設定會套用在**Proxy 主機名稱**層級，所有連線要求都要求的用戶端憑證。 客戶可以針對 Proxy 設定多達 20 個自訂網域 (中才支援**Premium**層) 和工作，這項限制。

