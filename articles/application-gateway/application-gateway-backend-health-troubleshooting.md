---
title: 針對 Azure 應用程式閘道中的後端健康情況問題進行疑難排解
description: 本文會引導您針對 Azure 應用程式閘道的後端健康情況問題進行疑難排解
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: ce1d0542530c4f190ace52d45e2369d6ecc18772
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384082"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>針對應用程式閘道中的後端健康情況問題進行疑難排解
==================================================

<a name="overview"></a>總覽
--------

應用程式閘道，預設會探查後端伺服器以檢查其健康情況狀態，以及是否準備好提供要求。 使用者也可以建立自訂探查，並提及主機名稱、要探查的路徑，以及要接受為狀況良好的狀態碼。 在這兩種情況下，如果後端伺服器未成功回應，應用程式閘道會將伺服器標示為狀況不良，並停止將要求轉送到伺服器。 一旦伺服器開始回應成功，它就會繼續提供要求。

### <a name="how-to-check-backend-health"></a>如何檢查後端健全狀況

若要檢查後端集區的健康情況，您可以使用 Azure 入口網站中的 [後端健康情況] 頁面。 或者，您可以使用[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0)、 [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)或[REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)來取得健全狀況狀態。 請針對每個方法檢查連結的文章，以取得詳細資訊。

上述任何方法所抓取的狀態可以是三種類型，也就是：

1.  狀況良好

2.  狀況不良

3.  未知

如果伺服器的後端健全狀況狀態為狀況良好，則表示應用程式閘道會將要求轉送到該伺服器。 但是，如果後端集區中所有伺服器的後端健康情況不良或不明，您可能會在應用程式存取期間遇到一些問題。 本檔說明後端伺服器可能狀況不良或不明時，所顯示之每個錯誤的徵兆、原因和解決方案。

<a name="backend-health-status-unhealthy"></a>後端健全狀況狀態不良
-------------------------------

如果後端健全狀況狀態顯示為 [狀況不良]，您會在入口網站上看到它，如下列螢幕擷取畫面所示：

![應用程式閘道後端健康情況-狀況不良](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

或者，如果您使用 Azure PowerShell、CLI 或 Azure REST API 查詢，您會看到類似下面的回應：
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
一旦後端集區中所有伺服器的後端伺服器狀態為 [狀況不良]，就不會將要求轉寄給它們，應用程式閘道會將502錯誤的閘道錯誤傳回給要求的用戶端。 若要疑難排解問題，請檢查 [後端健康情況] 索引標籤的 [詳細資料] 欄。

[後端健全狀況] 索引標籤的 [詳細資料] 欄中顯示的訊息，會提供有關此問題的詳細見解，並根據這些資訊，我們可以開始針對問題進行疑難排解。

> [!NOTE]
> 預設探查要求會以： <protocol> <port>//127.0.0.1 ../的格式傳送，例如， <http://127.0.0.1/>針對埠80上的 HTTP 探查，並只會將 HTTP 狀態碼200-399 的回應視為狀況良好的回應。 通訊協定和目的地埠會繼承自 HTTP 設定。 如果您想要應用程式閘道在不同的通訊協定、主機名稱或路徑上進行探查，並接受不同的狀態碼為狀況良好，請設定自訂探查，並將其與 HTTP 設定產生關聯。

### <a name="error-messages"></a>錯誤訊息

#### <a name="backend-server-timeout"></a>後端伺服器超時

**訊息：** 後端回應應用程式閘道\'健康情況探查所花費的時間，超過探查設定中的超時閾值。

**原因：** 一旦應用程式閘道將 HTTP （S）探查要求傳送到後端伺服器，它就會等待後端伺服器的回應設定一段時間。 如果後端伺服器未在超時值內回應，則會將它標示為狀況不良，直到它再次在超時時間內開始回應為止。

**解決方案：** 檢查後端伺服器或應用程式，以瞭解它在設定的超時時間內沒有回應的原因，並檢查應用程式相依性（例如，如果資料庫有任何問題，這可能會導致回應延遲）。 如果您知道應用程式的行為，而且它應該只在超時值之後回應，則請從自訂探查設定中增加超時值。
您需要有自訂探查，才能變更超時時間的值。如果您想要知道如何設定自訂探查，請[參閱檔頁面](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)。

若要增加超時時間，請遵循下列步驟：

1.  直接存取後端伺服器，並檢查伺服器在該頁面上回應所花費的時間。 您可以使用任何工具來存取，包括使用開發人員工具的瀏覽器。

2.  一旦您知道應用程式回應所花費的時間，請按一下 [健康情況探查] 索引標籤，然後選取與您的 HTTP 設定相關聯的探查。

3.  輸入比應用程式回應時間更高的超時值（以秒為單位）。

4.  儲存自訂探查設定，並檢查後端健全狀況（如果現在顯示狀況良好）。

#### <a name="dns-resolution-error"></a>DNS 解析錯誤

**訊息：** 應用程式閘道無法建立此後端的探查。 當後端的 FQDN 未正確輸入時，通常就會發生這種情況。 

**原因：** 如果後端集區的類型為 IP 位址/FQDN 或 App Service，應用程式閘道會解析為使用 DNS 伺服器（自訂或 Azure 預設）所輸入之 FQDN 的 IP 位址，並嘗試連接到 HTTP 設定中所述 TCP 埠上的伺服器。 但是，如果顯示此訊息，則表示應用程式閘道無法成功解析輸入之 FQDN 的 IP 位址。

**解決方案：**

1.  請確認在後端集區中輸入的 FQDN 是否正確，如果是公用網域，請嘗試從您的本機電腦加以解析。

2.  如果您可以解析 IP 位址，VNet 中的 DNS 設定可能會發生問題。

3.  檢查 VNet 是否已設定自訂 DNS 伺服器。 若是如此，請檢查 DNS 伺服器上是否無法解析為指定 FQDN 的 IP 位址。

4.  如果它是 Azure 預設 DNS，請洽詢您的網域註冊機構，確認是否已完成記錄或 CNAME 記錄對應。

5.  如果網域是私用/內部，請嘗試從相同 VNet 中的 VM 加以解析，如果您可以解決此問題，請重新開機應用程式閘道，然後再檢查一次。 若要重新開機應用程式閘道，您必須[停止](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0)並[開始](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0)使用個別檔連結中提供的 PowerShell 命令。

#### <a name="tcp-connect-error"></a>TCP 連接錯誤

**訊息：** 應用程式閘道無法連接至後端。
請檢查後端是否回應用於探查的埠。
同時檢查是否有任何 NSG/UDR/防火牆封鎖此後端的 Ip 和埠存取

**原因：** 在 DNS 解析階段之後，應用程式閘道會嘗試連線到 HTTP 設定中設定的 TCP 通訊埠上的後端伺服器。 如果應用程式閘道無法在指定的埠上建立 TCP 會話，此訊息就會將探查標示為狀況不良。

**解決方案：** 如果您看到此錯誤，請檢查下列各項：

1.  請檢查您是否可以使用瀏覽器或使用 PowerShell 連線到 HTTP 設定中所述埠上的後端伺服器，例如，您可以使用下列命令：Test-netconnection-ComputerName www.bing.com-Port 443

2.  如果所述的埠不是所需的埠，請輸入正確的埠號碼，讓應用程式閘道連接到後端伺服器

3.  如果您也無法從本機電腦連線至埠，則：

    a.  檢查後端伺服器的 NIC 和子網的 NSG 設定，並檢查是否允許連至所設定埠的輸入連接。 如果不允許，請建立新的規則以允許連接。 若要瞭解如何建立 NSG 規則，請[查看檔頁面](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)。

    b.  如果允許輸出公用和私用流量，請檢查應用程式閘道子網的 NSG 設定，以便進行連線。 請檢查（a）中連結的檔，以深入瞭解如何建立 NSG 規則。
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  檢查應用程式閘道和後端伺服器子網的 UDR 設定是否有任何路由異常。 請確定 UDR 不會將流量從後端子網導向出去。 例如，檢查網路虛擬裝置的路由，或透過 ExpressRoute/VPN 向應用程式閘道子網通告的預設路由。

    d.  若要檢查 NIC 的有效路由和規則，您可以使用下列 PowerShell 命令。
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  如果您在 NSG 或 UDR 中找不到任何問題，請檢查您的後端伺服器是否有應用程式相關問題，因為用戶端無法在設定的埠上建立 TCP 會話。 有幾件事需要檢查：

    e.  開啟命令提示字元（Win + R\> -cmd）並輸入 netstat，然後按 enter 鍵

    f.  檢查伺服器是否正在接聽已設定的埠。
        例如:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  如果不是，請檢查您的 web 伺服器設定，例如 IIS 中的網站系結、NGINX 中的伺服器區塊和 Apache 中的虛擬主機

    h.  檢查您的 OS 防火牆設定，以確定允許埠的連入流量

#### <a name="http-status-code-mismatch"></a>HTTP 狀態碼不符

**訊息：** 後端\'s HTTP 回應的狀態碼不符合探查設定。 預期：已收到 {HTTPStatusCode0}： {HTTPStatusCode1}。

**原因：** 一旦建立 TCP 連線並完成 SSL 交握（如果已啟用 SSL），應用程式閘道會將探查當做 HTTP GET 要求傳送至後端伺服器。 如先前所述，預設探查會\<是 protocol\>：//127.0.0.1：\<port\>/，而它會將開始風行200-399 中的回應狀態碼視為狀況良好。 如果伺服器傳回任何其他狀態碼，則會將此訊息標示為狀況不良。

**解決方案：** 視後端伺服器的回應碼而定，您可以採取下列步驟。 以下列出一些常見的狀態碼：

| **錯誤** | **動作** |
| --- | --- |
| 探查狀態碼不相符：收到401 | 檢查後端伺服器是否需要驗證。 應用程式閘道探查此時無法傳遞認證以進行驗證。 允許\"探查狀態碼\"中的 HTTP 401 符合或探查至伺服器不需要驗證的路徑。 | |
| 探查狀態碼不相符：收到403 | 禁止存取。 檢查後端伺服器上是否允許存取路徑。 | |
| 探查狀態碼不相符：收到404 | 找不到頁面。 如果後端伺服器可存取主機名稱路徑，請檢查它。 將 [主機名稱] 或 [路徑] 參數變更為可存取的值。 | |
| 探查狀態碼不相符：收到405 | 應用程式閘道的探查要求會使用 HTTP GET 方法。 檢查您的伺服器是否允許它。 | |
| 探查狀態碼不相符：收到500 | 內部伺服器錯誤。 檢查後端伺服器的健康情況，以及服務是否正在執行。 | |
| 探查狀態碼不相符：收到503 | 服務無法使用。 檢查後端伺服器的健康情況，以及服務是否正在執行。 | |

或者，如果您認為回應是合法，而您想要應用程式閘道接受其他狀態碼狀況良好，您可以建立自訂探查。 修改此項在後端網站需要驗證的情況下很有用，因為探查要求不會包含任何使用者認證，它們將會失敗，而且後端伺服器會傳回 HTTP 401 狀態碼。

若要建立自訂探查，請遵循[此處](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)所列的步驟。

#### <a name="http-response-body-mismatch"></a>HTTP 回應主體不符

**訊息：** 後端\'s HTTP 回應的主體與探查設定不相符。 收到的回應主體不包含 {string}。

**原因：** 當您建立自訂探查時，您可以藉由比對來自回應主體的字串，來將後端伺服器標示為狀況良好。 例如，您可以將應用程式閘道設定為接受「未經授權」，以符合的字串。 如果探查要求的後端伺服器回應包含「未經授權」字串，則會將它標示為狀況良好。
否則，它會標示為狀況不良，並顯示此訊息。

**解決方案：** 您可以遵循下列步驟來解決此問題：

1.  在本機或從探查路徑上的用戶端電腦存取後端伺服器，並檢查回應本文。

2.  檢查應用程式閘道的自訂探查設定，以確認回應本文是否符合設定的內容。

3.  如果不相符，請將具有正確字串值的探查設定變更為 [接受]。

您可以在[這裡](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)閱讀更多有關應用程式閘道探查比對的資訊。

#### <a name="backend-server-certificate-invalid-ca"></a>後端伺服器憑證不正確 CA

**訊息：** 後端所使用的伺服器憑證不是由知名的憑證授權單位單位（CA）所簽署。 上傳後端所使用伺服器憑證的根憑證，以將應用程式閘道上的後端列入白名單。

**原因：** 具有應用程式閘道 v2 的端對端 SSL 需要驗證後端伺服器的憑證，以將伺服器視為狀況良好。
若要讓 SSL 憑證受到信任，該後端伺服器的憑證必須由應用程式閘道的受信任存放區中所包含的 CA 發行。 如果憑證不是由受信任的 CA 所發行，例如自我簽署憑證，則使用者應該將簽發者的憑證上傳至應用程式閘道。

**解決方案：** 請遵循下列步驟，將受信任的根憑證匯出並上傳至應用程式閘道（以下提供的步驟適用于 Windows 用戶端）

1.  登入裝載您應用程式的電腦

2.  按 Win + R 或以滑鼠右鍵按一下 [開始] 按鈕，然後選取 [執行]，以開啟 [執行]

3.  輸入 certmgr.msc，然後按 Enter。 您也可以在 [開始] 功能表中搜尋 [憑證管理員]。

4.  尋找憑證，通常位於 [ \'憑證-目前的\\使用者\\個人\'憑證] 中，然後開啟憑證

5.  在 [憑證內容] 中，選取 [憑證路徑] 索引標籤

6.  選取 [根憑證]，然後選取 [查看憑證] 選項

7.  在 [憑證屬性] 中，切換至 [詳細資料] 索引標籤

8.  在 [詳細資料] 索引標籤中，選取 [複製到檔案] 選項，然後將檔案儲存在 Base-64 編碼的 x.509 （。CER）格式

9.  儲存之後，請在 Azure 入口網站中開啟 [應用程式閘道 HTTP 設定] 頁面

10. 開啟 HTTP 設定，然後按一下 [新增憑證]，並找出我們最近儲存的憑證檔案

11. 按一下 [儲存] 以儲存 HTTP 設定

或者，您可以從用戶端電腦匯出根憑證，方法是使用瀏覽器直接存取伺服器（略過應用程式閘道），並從瀏覽器匯出根憑證。

如需在應用程式閘道中解壓縮和上傳受信任的根憑證的詳細步驟，請參閱[這裡](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。

#### <a name="trusted-root-certificate-mismatch"></a>受信任的根憑證不相符

**訊息：** 後端所使用之伺服器憑證的根憑證，與新增至應用程式閘道的受信任根憑證不相符。 請確定您已新增正確的根憑證，以將後端列入允許清單

**原因：** 具有應用程式閘道 v2 的端對端 SSL 需要驗證後端伺服器的憑證，以將伺服器視為狀況良好。
若要讓 SSL 憑證受到信任，該後端伺服器的憑證必須由應用程式閘道的受信任存放區中所包含的 CA 發行。 如果憑證不是由受信任的 CA 所發行，例如自我簽署憑證，則使用者應該將簽發者的憑證上傳至應用程式閘道。

已上傳至應用程式閘道 HTTP 設定的憑證必須符合後端伺服器憑證的根憑證。

**解決方案：** 如果您看到上述的錯誤訊息，表示已經上傳至應用程式閘道的憑證與上傳到後端伺服器的憑證不相符。

請遵循上述的步驟1-11，將正確的受信任根憑證上傳至應用程式閘道。

如需在應用程式閘道中解壓縮和上傳受信任的根憑證的詳細步驟，請參閱[這裡](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)。
> [!NOTE]
> 如果後端伺服器未交換憑證的完整鏈，包括根 > 中繼（如果適用），則也可能會發生錯誤-在 TLS 信號交換期間 > 分葉。 若要驗證，您可以使用任何用戶端的 OpenSSL 命令，並使用應用程式閘道探查中設定的設定來連線到後端伺服器。

例如，
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
如果輸出未顯示所傳回憑證的完整鏈，請使用完整的鏈（包含根憑證）再次匯出憑證，並在您的後端伺服器中進行設定。 

已連接（00000188） \
深度 = 0 OU = 已驗證網域控制，CN \*=. example.com \
確認錯誤： num = 20：無法取得本機簽發者憑證 \
確認傳回： 1 \
深度 = 0 OU = 已驗證網域控制，CN \*=. example.com \
確認錯誤： num = 21：無法驗證第一個憑證 \
確認傳回： 1 \
\-\-\-\
憑證鏈 \
 0 s：/OU = 網域控制已驗證/CN = *。範例 .com \
   i：/C = US/ST = 亞利桑那/L = Scottsdale/O = GoDaddy .com，inc./ou =http://certs.godaddy.com/repository//CN=Go Daddy 安全憑證授權單位單位-G2 \
\-----開始憑證-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----結束憑證-----

#### <a name="backend-certificate-invalid-common-name-cn"></a>後端憑證不正確一般名稱（CN）

**訊息：** 後端憑證的一般名稱（CN）不符合探查的主機標頭。

**原因：** 應用程式閘道驗證後端 HTTP 設定中所指定的主機名稱是否與後端伺服器的 SSL 憑證所呈現的一般名稱（CN）相符。 這只是 Standard_v2 且 WAF_v2 的 SKU 行為。 標準和 WAF SKU 的 SNI 會設定為後端集區位址中的 FQDN。

在 v2 SKU 中，如果有預設探查（未設定/關聯任何自訂探查），則會從 HTTP 設定中所述的主機名稱，或在後端位址集區包含的 HTTP 設定中提及 [從後端位址挑選主機名稱]，來設定 SNI有效的 FQDN。

如果有與 HTTP 設定相關聯的自訂探查，則會從自訂探查設定中所述的主機名稱，或在自訂探查中核取 [從後端 HTTP 設定挑選主機名稱]，以從 HTTP 中所述的主機名稱來設定 SNI設置。

如果在 HTTP 設定中設定了「從後端位址挑選主機名稱」，則後端位址集區必須包含有效的 FQDN。

如果您看到上述的錯誤訊息，這表示後端憑證的一般名稱（CN）與自訂探查或 HTTP 設定中設定的主機名稱（如果選擇 [從後端 HTTP 設定挑選主機名稱]）不相符。 如果您使用預設探查，則主機名稱會設定為 "127.0.0.1"。 如果這不是所需的值，您應該建立自訂探查，並將其與 HTTP 設定產生關聯。

**解決方案：**

若要解決此問題，請遵循下列步驟：

若為 Windows：

1.  登入裝載您應用程式的電腦

2.  按 Win + R 或以滑鼠右鍵按一下 [開始] 按鈕，然後選取 [執行]，以開啟 [執行]

3.  輸入 certmgr.msc，然後按 Enter。 您也可以在 [開始] 功能表中搜尋 [憑證管理員]。

4.  尋找憑證，通常位於 [ \'憑證-目前的\\使用者\\個人\'憑證] 中，然後開啟憑證

5.  在 [詳細資料] 索引標籤中，檢查憑證的主旨

6.  從 [詳細資料] 確認憑證的 CN，並在自訂探查的 [主機名稱] 欄位或 HTTP 設定中輸入相同的值（如果選擇 [從後端 HTTP 設定挑選主機名稱]）。 如果這不是您的網站所需的主機名稱，您必須取得該網域的憑證，或在自訂探查/HTTP 設定中輸入正確的主機名稱。

針對使用 OpenSSL 的 Linux

1.  在 OpenSSL 中執行此命令 
```
openssl x509 -in certificate.crt -text -noout
```

2.  從顯示的屬性中，尋找憑證的 CN，並在 HTTP 設定的 [主機名稱] 欄位中輸入相同的值。 如果這不是您的網站所需的主機名稱，您必須取得該網域的憑證，或在自訂探查/HTTP 設定中輸入正確的主機名稱。

#### <a name="backend-certificate-is-invalid"></a>後端憑證無效

**訊息：** 後端憑證無效。 目前的日期不在憑證\"的有效\"來源\"和有效\"截止日期範圍內。

**原因：** 每個憑證都有有效性，而且 HTTPS 連線不安全，除非伺服器的 SSL 憑證有效。
目前的資料必須在有效的 from 和 valid 到範圍內。 如果不是，則會將憑證視為無效，而且會是安全性考慮。 此時，應用程式閘道會將後端伺服器標示為狀況不良。

**解決方案：** 如果您的 SSL 憑證已過期，請使用您的廠商更新憑證，並使用新的憑證補救伺服器設定。 如果它是自我簽署憑證，您必須產生有效的憑證，並將根憑證上傳至應用程式閘道的 HTTP 設定。 若要這麼做，請遵循下列步驟：

1.  在入口網站中開啟您的應用程式閘道 HTTP 設定

2.  選取具有過期憑證的 HTTP 設定，選取 [新增憑證]，然後開啟新的憑證檔案

3.  使用憑證旁的 [刪除] 圖示移除舊憑證，然後按一下 [儲存]

#### <a name="certificate-verification-failed"></a>憑證驗證失敗

**訊息：** 無法驗證後端憑證的有效性。 若要找出原因，請針對與錯誤碼 {errorCode} 相關聯的訊息，核取 [開啟 SSL 診斷]

**原因：** 當應用程式閘道無法驗證憑證的有效性時，就會發生此錯誤。

**解決方案：** 若要解決此問題，請在您的伺服器上檢查憑證是否已正確建立。 例如，您可以使用[OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html)來驗證憑證和其屬性，然後嘗試將憑證 reuploading 至應用程式閘道的 HTTP 設定。

<a name="backend-health-status-unknown"></a>後端健康狀態不明
-------------------------------
如果後端健康情況顯示為 [未知]，它會顯示在入口網站中，如下列螢幕擷取畫面所示：

![應用程式閘道後端健康情況-未知](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

萬一後端健康狀態顯示為 [未知]，可能是因為下列一個或多個原因所造成：

1.  應用程式閘道子網上的 NSG 會封鎖從 "Internet" 到埠65503-65534 （v1 SKU）或65200-65535 （v2 SKU）的輸入存取
2.  使用預設路由（0.0.0.0/0）在應用程式閘道子網上 UDR，而下一個躍點不是網際網路
3.  透過 BGP 對 VNet 的 ExpressRoute/VPN 連線所通告的預設路由
4.  VNet 中設定的自訂 DNS 伺服器無法解析公用功能變數名稱
5.  應用程式閘道處於狀況不良狀態

**解決方案：**

1.  檢查您的 NSG 是否封鎖從「網際網路」存取埠65503-65534 （v1 SKU）或65200-65535 （v2 SKU）

    a.  在應用程式閘道的 [總覽] 索引標籤中，選取 [虛擬網路/子網] 連結

    b.  在虛擬網路的 [子網] 索引標籤中，選取已部署應用程式閘道的子網

    c.  檢查是否有任何 NSG 已設定

    d.  如果有的話，請在 [搜尋] 索引標籤或 [所有資源] 底下搜尋該 NSG 資源

    e.  在 [輸入規則] 區段中，新增輸入規則以允許 v1 SKU 或 65200-65535 v2 SKU 的目的地埠範圍65503-65534，其來源為 [任何] 或 [網際網路]

    f.  按一下 [儲存]，並確認您是否可以成功地查看後端狀況良好

    g.  或者，您也可以透過[PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)執行此動作

2.  檢查您的 UDR 是否有預設路由（0.0.0.0/0），並將下一個躍點設為網際網路
    
    a.  遵循步驟 1. a 和 1. b 來判斷您的子網

    b.  檢查是否已設定任何 UDR。 如果有的話，請在搜尋列或 [所有資源] 底下搜尋資源

    c.  檢查是否有任何預設路由（0.0.0.0/0）的下一個躍點不是網際網路。 如果它是虛擬裝置或虛擬網路閘道，您必須確定您的虛擬裝置或內部部署裝置能夠適當地將封包路由回網際網路目的地，而不需要修改封包

    d.  否則，請將下一個躍點變更為 [網際網路]，然後按一下 [儲存]，並確認後端健康情況

3.  透過 BGP 對 VNet 的 ExpressRoute/VPN 連線所通告的預設路由

    a.  如果您有透過 BGP 對 VNet 的 ExpressRoute/VPN 連線，而且您正在公告預設路由，則必須確定封包會路由回到網際網路目的地，而不加以修改。

    b.  您可以使用應用程式閘道入口網站中的 [連線疑難排解] 選項來確認

    c.  將目的地手動選擇為任何網際網路可路由傳送的 IP 位址，例如 "1.1.1.1"，並將目的地埠當做任何專案，並檢查連線能力。

    d.  如果下一個躍點是虛擬網路閘道，則可能會透過 ExpressRoute 或 VPN 公告預設路由

4.  如果 VNet 中已設定自訂 DNS 伺服器，請確認伺服器是否可以解析公用網域。 當應用程式閘道必須與 OCSP 伺服器之類的外部網域，或檢查憑證的撤銷狀態等情況下，可能會需要公用功能變數名稱解析。

5.  若要確認應用程式閘道狀況良好且正在執行，請移至入口網站中的資源健康狀態選項，並確認其是否為「狀況良好」。 如果您看到狀況不良或已降級的狀態，請[聯絡支援人員](https://azure.microsoft.com/support/options/)。

<a name="next-steps"></a>後續步驟
----------

若要深入瞭解應用程式閘道診斷和記錄，請參閱[這裡](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)。
