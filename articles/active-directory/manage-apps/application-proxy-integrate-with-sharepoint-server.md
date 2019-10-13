---
title: 使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取 | Microsoft Docs
description: 涵蓋如何整合內部部署 SharePoint 伺服器與 Azure AD 應用程式 Proxy 的基本概念。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8f9cba1072866a3efdeb7b99981d0bfda22ab00
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286055"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取

此逐步指南說明如何將內部部署 SharePoint 伺服器陣列與 Azure Active Directory （Azure AD）應用程式 Proxy 進行整合。

## <a name="prerequisites"></a>必要條件

若要執行設定，您需要下列資源：
- SharePoint 2013 伺服器陣列或更新版本。
- 具有方案的 Azure Active Directory 租使用者，其中包含應用程式 Proxy。 深入瞭解[Azure Active Directory 方案和定價](https://azure.microsoft.com/pricing/details/active-directory/)。
- Azure AD 租使用者中[自訂驗證的網域](../fundamentals/add-custom-domain.md)。
- 內部部署 Active Directory 與可登[入 Azure](../hybrid/plan-connect-user-signin.md)的使用者 Azure AD Connect 同步處理。
- 在公司網域內的電腦上安裝並執行的應用程式 Proxy 連接器。

使用應用程式 Proxy 設定 SharePoint 需要兩個 Url：
- 外部 URL，使用者可以看見並在 Azure Active Directory 中決定。 此 URL 可能會使用自訂網域。 深入瞭解如何[在 Azure AD 應用程式 Proxy 中使用自訂網域](application-proxy-configure-custom-domain.md)。
- 內部 URL，只有在公司網域內才知道，而且永遠不會直接使用。

> [!IMPORTANT]
> 若要確保正確對應連結，請遵循下列適用于內部 URL 的建議：
> - 使用 HTTPS
> - 不使用自訂連接埠
> - 在公司 DNS 中，建立主機（A）以指向 SharePoint WFE （或負載平衡器），而不是別名（CName）

在本文中，將會使用下列值：
- 內部 URL： `https://sharepoint`
- 外部 URL： `https://spsites-demo1984.msappproxy.net/`
- SharePoint web 應用程式的應用程式集區帳戶： `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>步驟 1:在使用應用程式 Proxy 的 Azure AD 中設定應用程式

在此步驟中，您會在 Azure Active Directory 租使用者中建立應用程式，以使用應用程式 Proxy。 您將設定外部 URL，並指定內部 URL，並在稍後用於 SharePoint 中。

1. 依照下列設定所述，建立應用程式。 如需逐步指示，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   * **內部 URL**：稍後將在 SharePoint 中設定的 SharePoint 內部 URL，例如 `https://sharepoint`。
   * **預先驗證方法**：Azure Active Directory
   * **標頭中的轉譯 URL**：否
   * **轉譯應用程式主體中的 URL**：否

   ![將 SharePoint 發佈為應用程式](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 發佈您的應用程式之後，請按照下列步驟設定單一登入設定：

   1. 在入口網站的應用程式頁面上，選取 [單一登入]。
   1. 針對 [**單一登入模式]** ，選取 [**整合式 Windows 驗證**]。
   1. 將 [**內部應用程式 SPN** ] 設定為您稍早設定的值。 在此範例中，值會是 `HTTP/sharepoint`。
   1. 在 [**委派的登**入身分識別] 中，為您的 Active Directory 樹系設定選取最適合的選項。 例如，如果您的樹系中有單一 AD 網域，請選取 [內部**部署 SAM 帳戶名稱**] （如下所示），但如果您的使用者與 SharePoint 和應用程式 Proxy 連接器伺服器不在相同的網域中，請選取 [**內部部署使用者主體]名稱**（未顯示）。

   ![設定 SSO 的整合式 Windows 驗證](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 若要完成您的應用程式設定，請移至 [使用者和群組]區段，並指派使用者存取此應用程式。 

## <a name="step-2-configure-the-sharepoint-web-application"></a>步驟 2:設定 SharePoint web 應用程式

SharePoint web 應用程式必須使用 Kerberos 進行設定，適當的替代存取對應才能與 Azure AD 應用程式 Proxy 正常搭配運作。 有兩種可能的選項：

1. 建立新的 web 應用程式，並僅使用預設區域。 這是讓 SharePoint 獲得最佳體驗的慣用選項（例如，SharePoint 所產生的電子郵件警示中的連結一律會指向預設區域）。
1. 擴充現有的 web 應用程式，以在非預設區域上設定 Kerberos。

> [!IMPORTANT]
> 不論將使用的區域為何，SharePoint web 應用程式的應用程式集區帳戶都必須是網域帳戶，Kerberos 才能正常運作。

### <a name="provision-the-sharepoint-web-application"></a>布建 SharePoint web 應用程式

- 如果您建立新的 web 應用程式，而且只使用預設區域（偏好選項）：

    1. 啟動**SharePoint 管理命令**介面，然後執行下列腳本：

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    1. 開啟 [SharePoint 管理中心]網站。
    1. 在 [系統設定] 下，選取 [設定備用存取對應]。 [備用存取對應] 方塊隨即開啟。
    1. 使用新的 web 應用程式篩選顯示畫面，並確認您看到如下的內容：

       ![Web 應用程式的替代存取對應](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 如果您將現有的 web 應用程式擴充至新的區域（以防您無法使用預設區域）：

    1. 啟動**SharePoint 管理命令**介面，然後執行下列腳本：

       ```powershell
       # This scripts extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    1. 開啟 [SharePoint 管理中心]網站。
    1. 在 [系統設定] 下，選取 [設定備用存取對應]。 [備用存取對應] 方塊隨即開啟。
    1. 以已擴充的 web 應用程式篩選顯示，並確認您看到類似如下的內容：

        ![擴充應用程式的替代存取對應](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="ensure-that-the-sharepoint-web-application-is-running-under-a-domain-account"></a>確定 SharePoint web 應用程式是在網域帳戶下執行

使用下列步驟來識別執行 SharePoint web 應用程式之應用程式集區的帳戶，並確定它是網域帳戶：

1. 開啟 [SharePoint 管理中心]網站。
1. 移至 [安全性]，然後選取 [設定服務帳戶]。
1. 選取 [ **Web 應用程式集區-YourWebApplicationName**]。

   ![設定服務帳戶的選項](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. 確認 [**選取此元件的帳戶**] 會傳回網域帳戶，並記住在下一個步驟中需要用到它。

### <a name="ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>請確定 HTTPS 憑證已針對外部網路區域的 IIS 站台進行設定

因為內部 URL 使用 HTTPS 通訊協定（`https://SharePoint/`），所以必須在 IIS 網站中設定憑證。

1. 開啟 Windows PowerShell 主控台。
1. 執行下列程式碼，以產生自我簽署憑證，並將其新增至電腦 MY 存放區：

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 自我簽署憑證僅適合供測試使用。 在生產環境中，強烈建議使用由憑證授權單位發出的憑證。

1. 開啟「Internet Information Services 管理員」主控台。
1. 展開樹狀檢視中的伺服器及「網站」，選取站台的「SharePoint - AAD Proxy」，然後按一下 [繫結]。
1. 選取 https 繫結，然後按一下 [編輯...]。
1. 在 [SSL 憑證] 欄位中，選擇 [SharePoint]憑證，並按一下 [確定]。

您現在可以透過 Azure AD 應用程式 Proxy 從外部存取 SharePoint 網站。

## <a name="step-3-configure-kerberos-constrained-delegation-kcd"></a>步驟 3：設定 Kerberos 限制委派 (KCD)

使用者一開始會在 Azure Active Directory 中進行驗證，然後再透過 Azure AD Proxy 連接器使用 Kerberos 連線到 SharePoint。 若要允許連接器代表 Azure Active Directory 使用者取得 Kerberos 權杖，則必須使用通訊協定轉換來設定 Kerberos 限制委派。 若要深入了解 KCD，請參閱 [Kerberos 限制委派概觀](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))。

### <a name="set-the-service-principal-name-for-the-sharepoint-service-account"></a>設定 SharePoint 服務帳戶的服務主體名稱

在本文中，內部 URL 為 `https://sharepoint`，因此 SPN `HTTP/sharepoint`。 您必須以對應于您環境的值來取代這些值。
若要為 SharePoint 應用程式集區帳戶登錄 SPN `HTTP/sharepoint` `Contoso\spapppool`，請從命令提示字元執行下列命令，並以網域的系統管理員身分執行：

`setspn -S HTTP/sharepoint Contoso\spapppool`

Setspn 命令會先搜尋 SPN 再予以新增。 如果該檔案已經存在，您會看到**SPN 值重複**錯誤。 在此情況下，如果未在正確的應用程式集區帳戶下設定現有的 SPN，請考慮將它移除。  
您可以執行 Setspn 命令並搭配-L 選項，來確認已成功新增 SPN。 若要深入了解此命令，請參閱 [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))。

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>請確定連接器是受信任，並可委派加入至 SharePoint 應用程式集區帳戶的 SPN

設定 KCD，讓 Azure AD 應用程式 Proxy 服務可以將使用者識別委派給 SharePoint 應用程式集區帳戶。 啟用應用程式 Proxy 連接器來設定 KCD，以便為已在 Azure AD 中驗證的使用者擷取 Kerberos 票證。 然後該伺服器會將內容傳遞至目標應用程式，在此案例中即為 SharePoint。

若要設定 KCD，請針對每個連接器電腦重複下列步驟︰

1. 以網域系統管理員身分登入網域控制站，然後開啟**Active Directory 使用者和電腦**。
1. 尋找執行 Azure AD Proxy 連接器的電腦。 在此範例中，它是 SharePoint 伺服器本身。
1. 按兩下該電腦，然後按一下 [委派] 索引標籤。
1. 確定委派設定已設為 [只針對指定服務的委派信任這台電腦]。 然後，選取 [使用任何驗證通訊協定]。
1. 按一下 [**新增**] 按鈕，按一下 [**使用者或電腦**]，然後找出 SharePoint 應用程式集區帳戶，例如 `Contoso\spapppool`。
1. 在 SPN 的清單中，選取您稍早針對服務帳戶建立的 SPN。
1. 按一下 [確定]。 再按一下 [確定] 以儲存變更。
  
   ![委派設定](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

您現在已準備好使用外部 URL 登入 SharePoint，並向 Azure 進行驗證。

## <a name="troubleshoot-sign-in-errors"></a>對登入錯誤進行疑難排解

如果登入網站無法運作，您可以在連接器記錄中取得問題的詳細資訊：從執行連接器的電腦上，開啟 [事件檢視器]，移至 [**應用程式及服務記錄**檔]  > **Microsoft** > **AadApplicationProxy** > **connector**，然後檢查**管理**記錄檔。

## <a name="next-steps"></a>後續步驟

* [使用 Azure AD 應用程式 Proxy 中的自訂網域](application-proxy-configure-custom-domain.md)
* [了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)
