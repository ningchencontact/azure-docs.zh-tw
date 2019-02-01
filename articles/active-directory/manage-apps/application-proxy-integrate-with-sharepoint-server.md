---
title: 使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取 | Microsoft Docs
description: 涵蓋如何整合內部部署 SharePoint 伺服器與 Azure AD 應用程式 Proxy 的基本概念。
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: c2725b80c897ad07a2ae4245d9ef49c62fbfdc42
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203220"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取

本文討論如何將內部部署 SharePoint 伺服器與 Azure Active Directory (Azure AD) 應用程式 Proxy 整合。

若要啟用使用 Azure AD 應用程式 Proxy 對 SharePoint 的遠端存取，請遵循本逐步解說文件中的各節。

## <a name="prerequisites"></a>必要條件

本文假設您的環境中已經有 SharePoint 2013 或更新版本。 此外，請考慮下列必要條件︰

* SharePoint 包含 Kerberos 的原生支援。 因此，透過 Azure AD 應用程式 Proxy 遠端存取內部網站的使用者，應該可以有單一登入 (SSO) 體驗。

* 此案例包括 SharePoint Server 的組態變更。 建議您使用預備環境。 如此一來，您可以先更新預備伺服器，以便在進行測試週期後再進入生產環境。

* 我們需要已發佈的 URL 上的 SSL。 內部 URL 上也需要 SSL 以確保可正確地傳送/對應連結。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>步驟 1：設定 Kerberos 限制委派 (KCD)

對於使用 Windows 驗證的內部部署應用程式來說，您可以使用 Kerberos 驗證通訊協定和稱為 Kerberos 限制委派 (KCD) 的功能來達成單一登入 (SSO)。 KCD 一經設定，即可讓應用程式 Proxy 連接器為使用者取得 Windows 權杖，即使使用者並未直接登入 Windows 也是如此。 若要深入了解 KCD，請參閱 [Kerberos 限制委派概觀](https://technet.microsoft.com/library/jj553400.aspx)。

若要設定 SharePoint 伺服器的 KCD，請使用下列後續章節中的程序：

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>請確定 SharePoint Web 應用程式在網域帳戶下執行

首先，確定 SharePoint 是在網域帳戶下執行，而不是本機系統、本機服務或網路服務。 請務必確定這一點，您才可以將服務主體名稱 (SPN) 附加至帳戶。 SPN 是 Kerberos 通訊協定用來識別不同服務的方法。 稍候您需要使用該帳戶來設定 KCD。

> [!NOTE]
您必須擁有先前為服務建立的 Azure AD 帳戶。 建議您允許密碼自動變更。 如需完整的步驟和針對問題進行疑難排解的詳細資訊，請參閱[在 SharePoint 中設定自動密碼變更](https://technet.microsoft.com/library/ff724280.aspx)。

若要確定網站是根據所定義的服務帳戶來執行，請執行下列步驟︰

1. 開啟 [SharePoint 管理中心] 頁面。
2. 移至 [安全性]，然後選取 [設定服務帳戶]。
3. 選取 [Web 應用程式集區 - SharePoint - 80]。 根據 Web 集區的名稱或是否 Web 集區預設使用 SSL，選項可能會稍有不同。

  ![設定服務帳戶的選項](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. 如果 [選取此元件的帳戶] 欄位是設定為 [本機服務] 或 [網路服務]，則需要建立帳戶。 若不是，則您已完成，可以進行下一節。
5. 選取 [註冊新的受控帳戶]。 帳戶建立之後，您必須設定 [Web 應用程式集區] 才能使用該帳戶。

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>為 SharePoint 服務帳戶設定服務主體名稱

在設定 KCD 之前，您需要：

* 識別執行 Azure AD Proxy 將公開 SharePoint web 應用程式的網域帳戶。
* 選擇將在 Azure AD Proxy 和 SharePoint 中設定的內部 URL。 此內部 URL 必須不能已在 Web 應用程式中使用，而且絕不會出現在網頁瀏覽器中。

假設選擇的內部 URL 是 <https://sharepoint>，則 SPN 是：

```
HTTP/SharePoint
```

> [!NOTE]
> 請採用下列建議的內部 URL：
> * 使用 HTTPS
> * 不使用自訂連接埠
> * 在 DNS 中，建立指向 SharePoint WFE (或負載平衡器) 的主機 (A)，但非別名 (CName)

若要註冊此 SPN，請以網域系統管理員身分從命令提示字元執行下列命令：

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

此命令會設定 SharePoint 應用程式集區帳戶 _demo\spAppPoolAccount_ 的 SPN _HTTP/SharePoint_。

將您內部 URL 的 SPN 取代 _HTTP/SharePoint_ ， 以及將您環境中的應用程式集區帳戶取代 _demo\spAppPoolAccount_。 Setspn 命令會先搜尋 SPN 再予以新增。 若已經存在，您將會看到**重複的 SPN 值**錯誤。 在此情況下，如果未在正確的應用程式集區帳戶下設定，請考慮移除現有的 SPN。

您可以執行 Setspn 命令並搭配 -L 選項，來確認是否已新增 SPN。 若要深入了解此命令，請參閱 [Setspn](https://technet.microsoft.com/library/cc731241.aspx)。

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>請確定連接器是受信任，並可委派加入至 SharePoint 應用程式集區帳戶的 SPN

設定 KCD，讓 Azure AD 應用程式 Proxy 服務可以將使用者識別委派給 SharePoint 應用程式集區帳戶。 啟用應用程式 Proxy 連接器來設定 KCD，以便為已在 Azure AD 中驗證的使用者擷取 Kerberos 票證。 然後該伺服器會將內容傳遞至目標應用程式，在此案例中即為 SharePoint。

若要設定 KCD，請針對每個連接器電腦重複下列步驟︰

1. 以網域系統管理員身分登入 DC，然後開啟 **Active Directory 使用者和電腦**。
2. 尋找連接器執行所在的電腦。 在此範例中，它與 SharePoint 伺服器相同。
3. 按兩下該電腦，然後按一下 [委派] 索引標籤。
4. 確定委派設定已設為 [只針對指定服務的委派信任這台電腦]。 然後，選取 [使用任何驗證通訊協定]。
5. 按一下 [新增]按鈕，再按一下 [使用者或電腦]，並找出 SharePoint 應用程式集區帳戶，例如 _demo\spAppPoolAccount_。
6. 在 SPN 的清單中，選取您稍早針對服務帳戶建立的 SPN。
7. 按一下 [確定]。 再按一下 [確定] 以儲存變更。
  
  ![委派設定](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>步驟 2：設定 Azure AD Proxy

現在您已設定 KCD，就準備好要設定 Azure AD 應用程式 Proxy。

1. 請使用下列設定發佈您的 SharePoint 網站。 如需逐步指示，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](application-proxy-publish-azure-portal.md)。
   * **內部 URL**：稍早所選擇的 SharePoint 內部 URL，例如 **<https://SharePoint/>**。
   * **預先驗證方法**：Azure Active Directory
   * **轉譯標頭中的 URL**：否

   >[!TIP]
   >SharePoint 會使用 [主機標頭] 值來查閱網站。 它也會根據此值產生連結。 最後的結果是，SharePoint 所產生的任何連結都是已正確設定為使用外部 URL 的已發佈 URL。 將值設定為 [是] 也可讓連接器將要求轉送至後端應用程式。 不過，將值設定為 [否] 表示連接器不會傳送內部主機名稱。 相反地，連接器會傳送主機標頭作為對後端應用程式發佈的 URL。

   ![將 SharePoint 發佈為應用程式](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. 發佈您的應用程式之後，請按照下列步驟設定單一登入設定：

   1. 在入口網站的應用程式頁面上，選取 [單一登入]。
   2. 對於單一登入模式，選取 [整合式 Windows 驗證]。
   3. 將內部應用程式 SPN 設定為您先前設定的值。 在此範例中是 **HTTP/SharePoint**。
   4. 在「委派的登入身分識別」中，選取**內部部署 SAM 帳戶名稱**。

   ![設定 SSO 的整合式 Windows 驗證](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. 若要完成您的應用程式設定，請移至 [使用者和群組]區段，並指派使用者存取此應用程式。 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>步驟 3：設定 SharePoint 以使用 Kerberos 與 Azure AD Proxy URL

下一個步驟是擴充 SharePoint Web 應用程式到新的區域中，使用 Kerberos 以及適當的替代存取對應進行設定，以允許 SharePoint 處理傳送至內部 URL 的連入要求，並使用為外部 URL 建置的連結來回應。

1. 啟動 **SharePoint 管理介面**。
2. 執行下列指令碼以擴充 Web 應用程式到外部網路 區域，並啟用 Kerberos 驗證：

  ```powershell
  # Replace "http://spsites/" with the URL of your web application
  # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
  $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
  Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
  ```

3. 開啟 [SharePoint 管理中心] 網站。
4. 在 [系統設定] 下，選取 [設定備用存取對應]。 [備用存取對應] 方塊隨即開啟。
5. 選取您的網站，例如 **SharePoint - 80**。 目前為止，外部網路區域尚未有正確設定的內部 URL：

  ![[備用存取對應] 方塊](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. 按一下 [新增內部 URL] 。
7. 在 **URL 通訊協定、 主機和連接埠**文字方塊中，輸入 Azure AD Proxy 中設定的**內部 URL**，例如<https://SharePoint/>。
8. 在下拉式清單中，選取區域**外部網路**。
9. 按一下 [檔案] 。
10. 備用存取對應現在看起來應該像這樣：

  ![正確的備用存取對應](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>步驟 4：請確定 HTTPS 憑證已針對外部網路區域的 IIS 站台進行設定

SharePoint 組態現在已完成，但因為外部網路區域的內部 URL 是 <https://SharePoint/>，則必須為此網站設定憑證。

1. 開啟 Windows PowerShell 主控台。
2. 執行下列程式碼，以產生自我簽署憑證，並將其新增至電腦 MY 存放區：

  ```powershell
  # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
  New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
  ```

  > [!NOTE]
  自我簽署憑證僅適合供測試使用。 在生產環境中，強烈建議使用由憑證授權單位發出的憑證。

3. 開啟「Internet Information Services 管理員」主控台。
4. 展開樹狀檢視中的伺服器及「網站」，選取站台的「SharePoint - AAD Proxy」，然後按一下 [繫結]。
5. 選取 https 繫結，然後按一下 [編輯...]。
6. 在 [SSL 憑證] 欄位中，選擇 [SharePoint]憑證，並按一下 [確定]。

您現在可以透過 Azure AD 應用程式 Proxy 從外部存取 SharePoint 網站。

## <a name="next-steps"></a>後續步驟

* [使用 Azure AD 應用程式 Proxy 中的自訂網域](application-proxy-configure-custom-domain.md)
* [了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)
