---
title: 針對 Azure 應用程式閘道工作階段親和性的問題進行疑難排解
description: 本文章提供有關如何針對 Azure 應用程式閘道中的工作階段親和性問題進行疑難排解的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 66f61b5d6fcb86ed93e4dbae802ae7a80613c83d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66397845"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>針對 Azure 應用程式閘道工作階段親和性的問題進行疑難排解

了解如何診斷並解決與 Azure 應用程式閘道的工作階段親和性問題。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概觀

當您想要在同一個後端保留使用者工作階段時，以 Cookie 為基礎的工作階段親和性非常有用。 使用受閘道管理的 Cookie，應用程式閘道即可將來自使用者工作階段的後續流量導向至同一部伺服器進行處理。 當使用者工作階段的工作階段狀態儲存在伺服器本機時，這項功能很重要。

## <a name="possible-problem-causes"></a>可能發生問題的原因

中維護 cookie 型工作階段親和性的問題可能是由於下列主要原因：

- 未啟用 「 cookie 型同質性 」 設定
- 您的應用程式無法處理 cookie 型同質性
- 應用程式使用 cookie 型同質性，但仍反彈後端伺服器之間的要求

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>檢查是否已啟用 「 Cookie 型同質性 」 設定

有時候您忘了啟用 「 Cookie 型同質性 」 設定時，可能會發生的工作階段親和性的問題。 若要判斷您是否已啟用 「 Cookie 型同質性 」 上的設定在 Azure 入口網站中的 [HTTP 設定] 索引標籤，請依照下列指示：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在 **左瀏覽**窗格中，按一下**所有資源**。 按一下 [所有資源] 刀鋒視窗中的應用程式閘道名稱。 如果您已選取的訂用帳戶有幾個資源，您可以輸入中的應用程式閘道名稱**依名稱篩選...** 輕鬆地存取應用程式閘道。

3. 選取  **http-settings**索引標籤下**設定**。

   ![troubleshoot-session-affinity-issues-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 按一下  **appGatewayBackendHttpSettings**若要檢查您是否選擇右側**已啟用**以 Cookie 為主的親和性。

   ![troubleshoot-session-affinity-issues-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



您也可以檢查的值"**Backendhttpsettings**」 設定為*已啟用*下 「**backendHttpSettingsCollection**」 藉由使用下列方法之一：

- 執行[Get AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting)在 PowerShell 中
- 查看使用 Azure Resource Manager 範本 JSON 檔案

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>應用程式無法處理 cookie 型同質性

#### <a name="cause"></a>原因

應用程式閘道只能使用 cookie 來執行工作階段親和性。

#### <a name="workaround"></a>因應措施

如果應用程式無法處理 cookie 型同質性，您必須使用外部或內部 azure 負載平衡器或其他協力廠商解決方案。

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>應用程式使用 cookie 型同質性，但仍反彈後端伺服器之間的要求

#### <a name="symptom"></a>徵狀

當您在 Internet Explorer 中，例如使用簡短名稱的 URL 存取應用程式閘道時，您已啟用 Cookie 型同質性設定： [ http://website ](http://website/) ，要求仍會退回後端伺服器之間。

若要識別此問題，請依照下列指示：

1. 對 「 用戶端 」 連接到應用程式 Gateway(We are using Fiddler in this example) 後面的應用程式的 web 偵錯工具追蹤。
    **祕訣**如果您不知道如何使用 Fiddler，核取選項 」**我想要收集網路流量，並使用 web 偵錯工具進行分析**"底部。

2. 檢查和分析工作階段記錄檔，以判斷用戶端所提供的 cookie 是否具有 ARRAffinity 詳細資料。 如果您找不到 ARRAffinity 的詳細資訊，例如"**ARRAffinity =** *ARRAffinityValue*」 內的 cookie 集合，表示用戶端不回覆與 ARRA cookie，由所提供應用程式閘道。
    例如:

    ![troubleshoot-session-affinity-issues-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

應用程式會繼續嘗試在每個要求上設定 cookie，直到它取得回覆。

#### <a name="cause"></a>原因

Internet Explorer 和其他瀏覽器可能不會儲存或使用 cookie，具有簡短名稱的 URL，就會發生此問題。

#### <a name="resolution"></a>解決方案

若要修正此問題，建議使用 FQDN 來存取應用程式閘道。 例如，使用[ http://website.com ](https://website.com/)或是[ http://appgw.website.com ](http://appgw.website.com/) 。

## <a name="additional-logs-to-troubleshoot"></a>若要疑難排解的其他記錄檔

您可以收集其他記錄檔，並分析它們，以疑難排解問題相關的 cookie 型工作階段親和性

### <a name="analyze-application-gateway-logs"></a>分析應用程式閘道記錄檔

若要收集應用程式閘道記錄檔，請依照下列指示：

透過 Azure 入口網站啟用記錄功能

1. 在  [Azure 入口網站](https://portal.azure.com/)，尋找您的資源，然後按一下**診斷記錄**。

   應用程式閘道有三個記錄：存取記錄、 效能記錄、 防火牆記錄檔

2. 若要開始收集資料，請按一下**開啟診斷**。

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. [診斷設定]  刀鋒視窗中提供診斷記錄的設定。 在此範例中，Log Analytics 會儲存記錄。 按一下 **設定**下方**Log Analytics**來設定您的工作區。 您也可以使用事件中樞和儲存體帳戶來儲存診斷記錄。

   ![troubleshoot-session-affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 確認設定，然後按一下**儲存**。

   ![troubleshoot-session-affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>檢視和分析應用程式閘道存取記錄

1. 在 Azure 入口網站應用程式閘道資源檢視 下，選取**診斷記錄**中**監視**一節。

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 在右側，選取 「**ApplicationGatewayAccessLog**「 之下的下拉式清單中**記錄類別。**  

   ![troubleshoot-session-affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. 在應用程式閘道存取記錄檔 清單中，按一下您要分析及匯出，記錄的檔，然後將匯出的 JSON 檔案。

4. 轉換您匯出至 CSV 檔案的步驟 3 中的 JSON 檔案，並在 Excel、 Power BI 中或任何其他資料視覺化工具中檢視它們。

5. 請檢查下列資料：

- **ClientIP**– 這是從連接的用戶端的用戶端 IP 位址。
- **ClientPort** -這是從連接的用戶端要求的來源連接埠。
- **RequestQuery** – 這表示已收到的要求在目的地伺服器。
- **Server-routed**：會接收到要求的後端集區執行個體。
- **X-AzureApplicationGateway-LOG-ID**：要求所使用的相互關聯識別碼。 它可以用來針對後端伺服器上的流量問題進行疑難排解。 例如: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**：應用程式閘道從後端收到的 HTTP 回應碼。

  ![troubleshoot-session-affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

如果您看到兩個項目來自相同的 ClientIP 和用戶端連接埠，而且傳送到相同的後端伺服器，這表示應用程式閘道設定正確。

如果您看到兩個項目來自相同的 ClientIP 和用戶端連接埠，而且傳送到不同的後端伺服器，這表示該要求會退回選取的後端伺服器之間"**應用程式正在使用但要求的 cookie 型同質性後端伺服器之間仍反彈**"底部進行疑難排解。

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>使用 web 偵錯工具來擷取及分析 HTTP 或 HTTPS 流量

Web 偵錯 Fiddler 等工具，可協助您偵錯 web 應用程式擷取在網際網路和測試的電腦之間的網路流量。 這些工具可讓您檢查傳入和傳出的資料，因為瀏覽器接收/傳送它們。 Fiddler，在此範例中，有 HTTP 重新執行選項，可協助您針對與 web 應用程式，特別是針對問題的驗證種類的用戶端問題進行疑難排解。

使用您選擇的 web 偵錯工具。 在此範例中，我們將使用 Fiddler 擷取和分析 http 或 https 流量，請依照下列指示：

1. 下載在 Fiddler 工具<https://www.telerik.com/download/fiddler>。

    > [!NOTE]
    > 如果擷取的電腦已安裝.NET 4，請選擇 [Fiddler4]。 否則，請選擇 Fiddler2。

2. 以滑鼠右鍵按一下 安裝程式可執行檔，並安裝的系統管理員身分執行。

    ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. 當您開啟 Fiddler 時，它應該會自動開始擷取流量 （請注意在較低左下角擷取）。 按下 F12 以啟動或停止擷取流量。

    ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 最有可能您感興趣的解密 HTTPS 流量，以及您可以選取，以啟用 HTTPS 解密**工具** > **Fiddler 選項**，然後核取方塊"**解密HTTPS 流量**"。

    ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 您可以移除先前不相關的工作階段，才能重現問題，依序按一下**X** （圖示） >**全部移除**螢幕擷取畫面如下： 

    ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 一旦您有重現問題，請選取儲存檔案供檢閱**檔案** > **儲存** > **所有工作階段...** . 

    ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 請檢查並分析的工作階段記錄檔以判定出問題。

    例如：

- **範例 a:** 您找到工作階段記錄檔，從用戶端所傳送的要求，並移至應用程式閘道的公用 IP 位址，請按一下此記錄檔，以檢視詳細資料。  在右側中，在下方方塊中的資料會是應用程式閘道傳回至用戶端。 選取 ["RAW"] 索引標籤，然後判斷是否要接收用戶端 」**Set-cookie:ARRAffinity=** *ARRAffinityValue*." 如果沒有任何 cookie，未設定工作階段親和性，或應用程式閘道不套用至用戶端的 cookie。

   > [!NOTE]
   > 應用程式閘道設定為傳送到特定的後端伺服器的用戶端的 cookie-識別碼，為此 ARRAffinity 值。

   ![troubleshoot-session-affinity-issues-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **範例 b:** 下一步的工作階段記錄檔是由先前的其中一個是用戶端回到應用程式閘道上設定 ARRAAFFINITY 回應項目所後面。 如果 ARRAffinity cookie 識別碼符合，就應該封包傳送到先前使用的相同後端伺服器。 檢查 http 通訊，以查看是否要變更用戶端的 ARRAffinity cookie 接下來的幾的行。

   ![troubleshoot-session-affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 相同的通訊工作階段 cookie 應該不變更。 檢查右側頂端的方塊中，選取 [Cookie] 索引標籤，查看用戶端時是否使用 cookie，並將其傳回到應用程式閘道。 否則，不讓用戶端瀏覽器，以及使用 cookie 的交談。 有時候，可能位於用戶端。

 

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。
