---
title: 新增內部部署應用程式：Azure Active Directory 中的應用程式 Proxy | Microsoft Docs
description: Azure Active Directory (Azure AD) 有一項應用程式 Proxy 服務，可讓使用者使用其 Azure AD 帳戶登入來存取內部部署應用程式。 本教學課程會示範如何準備環境以便與應用程式 Proxy 搭配使用，然後會使用 Azure 入口網站將內部部署應用程式新增至 Azure AD 租用戶。
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: ed275638c1b217f18d5d1335b9d039879ae55004
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846299"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>教學課程：新增內部部署應用程式以便透過 Azure Active Directory 中的應用程式 Proxy 進行遠端存取

Azure Active Directory (Azure AD) 有一項應用程式 Proxy 服務，可讓使用者使用其 Azure AD 帳戶登入來存取內部部署應用程式。 本教學課程會準備環境以便與應用程式 Proxy 搭配使用。 環境準備就緒後，您會使用 Azure 入口網站將內部部署應用程式新增至 Azure AD 租用戶。

本教學課程會：

> [!div class="checklist"]
> * 對輸出流量開啟連接埠，並允許存取特定 URL。
> * 在 Windows 伺服器上安裝連接器，並向應用程式 Proxy 註冊。
> * 確認連接器的安裝和註冊是否正確。
> * 將內部部署應用程式新增至 Azure AD 租用戶。
> * 確認測試使用者可以使用 Azure AD 帳戶登入應用程式。

## <a name="before-you-begin"></a>開始之前

若要將應用程式新增至您的租用戶，您需要：

* [Microsoft Azure AD 基本或進階訂用帳戶](https://azure.microsoft.com/pricing/details/active-directory)。 
* 應用程式系統管理員帳戶。

### <a name="windows-server"></a>Windows 伺服器
若要使用應用程式 Proxy，您需要執行 Windows Server 2012 R2 或更新版本的 Windows 伺服器。 您會在伺服器上安裝應用程式 Proxy 連接器。 此連接器伺服器需要連線至 Azure 中的「應用程式 Proxy」服務，以及您打算發佈的內部部署應用程式。

若要在生產環境中實現高可用性，建議您準備多個 Windows 伺服器。 在本教學課程中，一部 Windows 伺服器就已足夠。

**針對連接器伺服器的建議**

1. 將連接器伺服器實體放在靠近應用程式伺服器的位置，以便連接器和應用程式之間能有最佳效能。 如需詳細資訊，請參閱[網路拓撲考量](application-proxy-network-topology.md)。

2. 連接器伺服器和 Web 應用程式伺服器應該屬於相同的 Active Directory 網域。 伺服器必須位於相同網域，才能搭配使用單一登入 (SSO) 與整合式 Windows 驗證 (IWA) 和 Kerberos 限制委派 (KCD) 的需求。 如果連接器伺服器和 Web 應用程式伺服器位於不同的 Active Directory 網域，則必須使用以資源為基礎的委派才能實現單一登入。 如需詳細資訊，請參閱[使用應用程式 Proxy 進行單一登入的 KCD](application-proxy-configure-single-sign-on-with-kcd.md)。

**軟體需求**

您安裝「應用程式 Proxy」連接器之前，Windows 連接器伺服器需要先啟用 TLS 1.2。 版本低於 1.5.612.0 的現有連接器可以繼續在先前的 TLS 版本上運作，直到進一步通知。 

啟用 TLS 1.2：

1. 設定下列登錄機碼：
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. 重新啟動伺服器

  
## <a name="prepare-your-on-premises-environment"></a>準備內部部署環境
若要準備適合 Azure AD 應用程式 Proxy 的環境，您需要先啟用 Azure 資料中心的通訊。 如果路徑中有防火牆，請確定防火牆已開啟，如此連接器才能對「應用程式 Proxy」提出 HTTPS (TCP) 要求。

### <a name="open-ports"></a>開啟連接埠

對**輸出**流量開啟下列連接埠。 

   | 連接埠號碼 | 使用方式 |
   | --- | --- |
   | 80 | 下載憑證撤銷清單 (CRL) 時驗證 SSL 憑證 |
   | 443 | 應用程式 Proxy 服務的所有傳出通訊 |

如果您的防火牆根據原始使用者強制執行流量，也請針對來自以網路服務形式執行的 Windows 服務的流量，開啟連接埠 80 和 443。

如果您已經在使用應用程式 Proxy，則可能已安裝較舊版本的連接器。  請遵循此教學課程，以安裝最新版的連接器。 早於 1.5.132.0 的版本也需要開啟下列連接埠：5671、8080、9090-9091、9350、9352、10100–10120。 

### <a name="allow-access-to-urls"></a>允許存取 URL

允許存取下列 URL：

| URL | 使用方式 |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | 連接器和應用程式 Proxy 雲端服務之間的通訊 |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure 會使用這些 URL 來確認憑證 |
| login.windows.net<br>login.microsoftonline.com | 連接器會在註冊程序進行期間使用這些 URL。 |

如果您的防火牆或 Proxy 允許建立 DNS 允許清單，您可以建立 \*.msappProxy.net 和 \*.servicebus.windows.net 的允許清單連線。 若非如此，您需要允許存取 [Azure 資料中心的 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 IP 範圍會每週更新。

## <a name="install-and-register-a-connector"></a>安裝並註冊連接器
若要使用應用程式 Proxy，您必須在選擇與應用程式 Proxy 服務搭配使用的每一部 Windows 伺服器上安裝連接器。 連接器會作為代理程式來管理從內部部署應用程式伺服器到 Azure AD 中應用程式 Proxy 的輸出連線。 在同時安裝了其他驗證代理程式 (例如，Azure AD Connect) 的伺服器上，您也可以安裝連接器。

若要安裝連接器：

1. 以目錄 (使用應用程式 Proxy) 的應用程式管理員身分，登入 [Azure 入口網站](https://portal.azure.com/)。 例如，如果租用戶網域為 contoso.com，則系統管理員應該是 admin@contoso.com ，或該網域上的其他管理員別名。
2. 您目前的目錄會顯示在右上角的使用者名稱之下。 請確認您已登入使用應用程式 Proxy 的目錄。 如果您需要變更目錄，請選取該圖示。
3. 在左側的刀鋒視窗中，按一下 [Azure Active Directory]，然後按一下 [應用程式 Proxy]。
4. 按一下 [下載連接器服務]。
5. 閱讀服務條款。  當您準備好時，按一下 [接受條款並下載]。
6. 在視窗底部，您會看到提示要求您下載 **AADApplicationProxyConnectorInstaller.exe**。 按一下 [執行] 以安裝連接器。 安裝精靈隨即開啟。 
7. 依照精靈中的指示進行安裝。 當系統提示您向 Azure AD 租用戶的應用程式 Proxy 註冊連接器時，請提供您的應用程式管理員認證。
    - 針對 Internet Explorer (IE)，如果 [IE 增強式安全性設定] 設定為 [開啟]，您可能不會看到註冊畫面。 若要取得存取權，請依照錯誤訊息中的指示。 請確定 [Internet Explorer 增強式安全性] 已設定為 [關閉]。

### <a name="general-remarks"></a>一般備註

如果您先前已安裝連接器，請重新安裝以取得最新版本。

如果您選擇為內部部署應用程式準備多個 Windows 伺服器，則必須在每一部伺服器上安裝和註冊連接器。 您可以將連接器組織成連接器群組。 如需詳細資訊，請參閱[連接器群組](application-proxy-connector-groups.md)。 

如果您的組織使用 Proxy 伺服器連線到網際網路，您需要為它們設定應用程式 Proxy。  如需詳細資訊，請參閱[使用現有的內部部署 Proxy 伺服器](application-proxy-configure-connectors-with-proxy-servers.md)。 

如需有關連接器、容量規劃以及其如何保持最新狀態的相關資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)。 

如果您使用 Qlik Sense 應用程式，則一律安裝最新的連接器。 Qlik Sense 會使用 WebSockets，其僅在連接器 1.5.612.0 版或更新版本提供支援。


## <a name="verify-the-connector-installed-and-registered-correctly"></a>確認連接器的安裝和註冊是否正確

您可以使用 Azure 入口網站或 Windows 伺服器來確認新的連接器是否安裝正確。

### <a name="verify---azure-portal"></a>確認 - Azure 入口網站
若要確認連接器的安裝和註冊是否正確：

1. 在 [Azure 入口網站](https://portal.azure.com)中登入租用戶目錄。
2. 按一下 [Azure Active Directory]，然後按一下 [應用程式 Proxy]。 所有的連接器與連接器群組都會出現在此頁面上。 
3. 選取連接器以確認其詳細資料。 作用中的綠色標籤會指出連接器可以連線至服務。 不過，即使標籤是綠色的，仍可能會因為網路問題而讓連接器無法接收訊息。 

    ![Azure AD 應用程式 Proxy 連接器](./media/application-proxy-connectors/app-proxy-connectors.png)

如需如何安裝連接器的詳細說明，請參閱[在安裝應用程式 Proxy 連接器時發生問題](application-proxy-connector-installation-problem.md)。

### <a name="verify---windows-server"></a>確認 - Windows 伺服器
若要確認連接器的安裝和註冊是否正確：

1. 按一下 [Windows] 機碼並輸入 services.msc 來開啟 Windows 服務管理員。
2. 確認下列兩項服務的狀態是否為**執行中**。
   - **Microsoft AAD 應用程式 Proxy 連接器** 可啟用連線
   - **Microsoft AAD 應用程式 Proxy 連接器更新程式**是自動更新服務。 更新程式會檢查連接器的新版本，並且視需要更新連接器。

    ![應用程式 Proxy 連接器服務 - 螢幕擷取畫面](./media/application-proxy-enable/app_proxy_services.png)

3. 如果服務的狀態不是**執行中**，請對每個服務按一下滑鼠右鍵，然後選擇 [啟動]。 

## <a name="add-an-on-premises-app-to-azure-ad"></a>將內部部署應用程式新增至 Azure AD

現在您已準備好環境並安裝好連接器，接下來您便可以將內部部署應用程式新增至 Azure AD。  

1. 在 [Azure 入口網站](https://portal.azure.com/)中，以系統管理員身分登入。
2. 選取 [Azure Active Directory]  >  [企業應用程式]  >  [新增應用程式]。

    ![新增企業應用程式](./media/application-proxy-publish-azure-portal/add-app.png)

3. 依序選取 [所有]、[內部部署應用程式]。  

    ![新增您自己的應用程式](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 在 [新增自己的內部部署應用程式] 刀鋒視窗中，提供您應用程式的下列資訊：

    ![設定您的應用程式](./media/application-proxy-publish-azure-portal/configure-app.png)

    | 欄位 | 說明 |
    | :---- | :---------- |
    | **名稱** | 會出現在存取面板上和 Azure 入口網站的應用程式名稱。 |
    | **內部 URL** | 用於從私用網路內部存取應用程式的 URL。 您可以提供後端伺服器上要發佈的特定路徑，而伺服器的其餘部分則不發佈。 如此一來，您可以在相同的伺服器上將不同網站發佈為不同應用程式，並給予各自的名稱和存取規則。<br><br>如果您發佈路徑，請確定其中包含您的應用程式的所有必要映像、指令碼和樣式表。 例如，如果您的應用程式位於 https://yourapp/app 並使用位於 https://yourapp/media 的映像，您應該發佈 https://yourapp/ 作為路徑。 此內部 URL 不一定是您使用者所看見的登陸頁面。 如需詳細資訊，請參閱[針對發佈應用程式設定自訂的首頁](application-proxy-configure-custom-home-page.md)。 |
    | **外部 URL** | 可讓使用者從網路外部存取應用程式的位址。 如果您不想使用預設的應用程式 Proxy 網域，請閱讀 [Azure AD Application Proxy 中的自訂網域](application-proxy-configure-custom-domain.md)。|
    | **預先驗證** | 應用程式 Proxy 在給予您的應用程式存取權前，用來驗證使用者的方式。<br><br>**Azure Active Directory** - 應用程式 Proxy 會重新導向使用者以使用 Azure AD 登入，進而驗證目錄和應用程式的權限。 建議您將這個選項保持為預設值，讓您可以利用諸如條件式存取以及 Multi-Factor Authentication 等 Azure AD 安全性功能。 利用 Microsoft Cloud Application Security 監視應用程式時需要 **Azure Active Directory**。<br><br>**即時通行** - 使用者不必向 Azure Active Directory 進行驗證即可存取應用程式。 您還是可以在後端設定驗證需求。 |
    | **連接器群組** | 連接器會處理針對應用程式的遠端存取，連接器群組可協助您依區域、網路或用途組織連接器和應用程式。 如果您尚未建立任何連接器群組，您的應用程式就會指派給 [預設]。<br><br>如果應用程式使用 WebSocket 進行連線，群組中的所有連接器必須是 1.5.612.0 版或更新版本。|

5. 如有必要，請設定 [其他設定]。 對於大部分的應用程式，您應該在其預設狀態中保留這些設定。 

    | 欄位 | 說明 |
    | :---- | :---------- |
    | **後端應用程式逾時** | 只有當您的應用程式太慢而無法驗證和連線時，才將此值設定為 [長]。 |
    | **使用僅限 HTTP Cookie** | 將此值設定為 [是]，讓應用程式 Proxy Cookie 在 HTTP 回應標頭中包含 HTTPOnly 旗標。 如果使用遠端桌面服務，請將此值設定為 [否]。|
    | **使用安全的 Cookie**| 將此值設定為 [是]，以透過安全的通道 (例如加密的 HTTPS 要求) 傳輸 Cookie。
    | **使用永續性 Cookie**| 將此值的設定保留為 [否]。 只有無法在程序之間共用 Cookie 的應用程式，才應使用此設定。 如需 Cookie 設定的詳細資訊，請參閱 [Azure Active Directory 中用來存取內部部署應用程式的 Cookie 設定](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)
    | **轉譯標頭中的 URL** | 除非您的應用程式需要驗證要求中的原始主機標頭，否則請將此值保留為 [是]。 |
    | **轉譯應用程式主體中的 URL** | 除非您有其他內部部署應用程式的硬式編碼 HTML 連結，且未使用自訂網域，否則請將此值保留為 [否]。 如需詳細資訊，請參閱[使用應用程式 Proxy 連結轉譯](application-proxy-configure-hard-coded-link-translation.md)。<br><br>如果您打算使用 Microsoft Cloud App Security (MCAS) 監視此應用程式，請將此值設定為 [是]。 如需詳細資訊，請參閱[使用 Microsoft Cloud App Security 與 Azure Active Directory 設定即時應用程式存取監視](application-proxy-integrate-with-microsoft-cloud-application-security.md) |
   


6. 選取 [新增] 。

## <a name="test-the-application"></a>測試應用程式

您準備好測試應用程式是否已正確新增。 在下列步驟中，您會將使用者帳戶新增至應用程式，然後嘗試登入。

### <a name="add-a-user-for-testing"></a>新增測試使用者
在將使用者新增至應用程式之前，請確認使用者帳戶已經有權限可存取公司網路內部的應用程式。

若要新增測試使用者：

1. 在 [快速啟動] 刀鋒視窗中，選取 [指派測試使用者]。

    ![指派測試使用者](./media/application-proxy-publish-azure-portal/assign-user.png)

2. 在 [使用者和群組] 刀鋒視窗上，選取 [新增使用者]。

    ![新增使用者或群組](./media/application-proxy-publish-azure-portal/add-user.png)

3. 在 [新增指派] 刀鋒視窗上，選取 [使用者和群組]，然後選擇您要新增的帳戶。 
4. 選取 [指派]。

### <a name="test-the-sign-on"></a>測試登入

若要測試能否登入應用程式：

1. 在瀏覽器中瀏覽至您在發行步驟所設定的外部 URL。 
2. 您應會看見開始畫面。
3. 嘗試以您在上一節中建立的使用者身分登入。

    ![測試已發佈的應用程式](./media/application-proxy-publish-azure-portal/test-app.png)

如需疑難排解，請參閱[針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已讓內部部署環境準備好與應用程式 Proxy 搭配運作，然後安裝並註冊了應用程式 Proxy 連接器。 接下來，您將應用程式新增至 Azure AD 租用戶。 您已確認使用者可以使用 Azure AD 帳戶登入應用程式。

您進行了下列事項：
> [!div class="checklist"]
> * 對輸出流量開啟連接埠，並允許存取特定 URL
> * 在 Windows 伺服器上安裝連接器，並向應用程式 Proxy 註冊
> * 確認連接器的安裝和註冊是否正確
> * 將內部部署應用程式新增至 Azure AD 租用戶
> * 確認測試使用者可以使用 Azure AD 帳戶登入應用程式。

您可以為應用程式設定單一登入。 使用下列連結來選擇單一登入方法，以及尋找單一登入教學課程。 

> [!div class="nextstepaction"]
>[設定單一登入](what-is-single-sign-on.md#choosing-a-single-sign-on-method)





