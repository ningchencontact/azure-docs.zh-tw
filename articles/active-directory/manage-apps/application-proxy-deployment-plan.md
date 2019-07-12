---
title: 規劃 Azure Active Directory 應用程式 Proxy 部署
description: 規劃您的組織內的應用程式 proxy 部署的端對端指南
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: 7d40c0604f0947abe8d536eafe87545790476a98
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625538"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>規劃 Azure AD 應用程式 Proxy 部署

Azure Active Directory (Azure AD) 應用程式 Proxy 是在內部部署應用程式的安全且符合成本效益的遠端存取解決方案。 它提供立即轉換的路徑來管理存取權舊版的 「 雲端第一個 「 組織內部尚無法使用新型的通訊協定的應用程式。 如需詳細的簡介資訊，請參閱[什麼是應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。

應用程式 Proxy 並建議能讓遠端使用者存取內部資源。 VPN 的需求或反向 proxy，在這些遠端存取使用情況下的，會取代應用程式 Proxy。 它不是位於公司網路的使用者。 這些應用程式 Proxy 用於內部網路存取的使用者可能會遇到非預期的效能問題。

本文包含計劃、 操作及管理 Azure AD 應用程式 Proxy 所需的資源。 

## <a name="plan-your-implementation"></a>計劃您的實作

下一節提供廣泛的檢視，計劃會設定以進行有效率的部署經驗的項目索引鍵。 

### <a name="prerequisites"></a>先決條件

您必須符合下列必要條件再開始您的實作。 您可以看到的詳細資訊，設定您的環境，包括這些先決條件後，在此[教學課程](application-proxy-add-on-premises-application.md)。

* **連接器**:連接器是輕量級代理程式，您可以將它部署到：
   * 實體硬體內部
   * 在 hypervisor 中的任何方案內裝載的 VM
   * 若要啟用應用程式 Proxy 服務的輸出連線的 Azure 中裝載的 VM。

* 請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)如需更詳細的概觀。

     * 連接器的電腦上必須[啟用 TLS 1.2](application-proxy-add-on-premises-application.md)再安裝連接器。

     * 可能的話，部署中的連接器[相同的網路](application-proxy-network-topology.md)與做為後端 web 應用程式伺服器的區段。 最好的方式是在您完成應用程式的探索後，部署連接器。
     * 我們建議每個連接器群組有兩個以上的連接器，可提供高可用性和延展性。 如果您可能需要以服務的機器，在任何時間點，有三個連接器是最佳作法。 檢閱[連接器容量資料表](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning)來協助決定什麼類型的機器上安裝連接器。 越大的機器的多個緩衝區和高效能會使用連接器。

* **網路存取設定**:Azure AD 應用程式 Proxy 連接器[透過 HTTPS (TCP 連接埠 443) 和 HTTP (TCP 連接埠 80) 連線到 Azure](application-proxy-add-on-premises-application.md)。 

   * 終止連接器 TLS 流量不受支援，並可防止連接器建立具有其各自的 Azure 應用程式 Proxy 端點的安全通道。

   * 避免所有形式的內嵌檢查輸出連接器與 Azure 之間的 TLS 通訊。 連接器和後端的應用程式之間的內部檢查是可行的但是可能會降低使用者體驗，而且此情況下，不建議。

   * 連接器本身的負載平衡也不是受支援，或甚至是必要。

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>設定 Azure AD Application Proxy 之前的重要考量

必須符合下列的核心需求，才能設定及實作 Azure AD 應用程式 Proxy。

*  **Azure 上架**:在部署應用程式 proxy 時，必須從內部部署目錄同步處理或直接在您的 Azure AD 租用戶內建立使用者身分識別。 身分識別同步處理可讓 Azure AD 在允許使用者存取 App Proxy 發佈的應用程式之前，先預先驗證使用者，以及具有執行單一登入 (SSO) 所需的使用者識別碼資訊。

* **條件式存取需求**:不建議使用內部網路存取的應用程式 Proxy，因為這樣會影響使用者的延遲。 我們建議使用預先驗證和條件式存取原則的應用程式 Proxy，來自網際網路的遠端存取。  使用 AAD 進行驗證的方式，使用於內部網路的條件式存取是現代化的應用程式，因此他們可以 diretly。 請參閱[資源移轉至 AAD 的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)如需詳細資訊。 

* **服務限制**:若要防止使用量過高的個別租用戶的資源是設定每個應用程式和租用戶的節流限制。 若要查看這些限制，請參閱[Azure AD 服務限制](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)。 這些節流限制會根據遠超出一般使用磁碟區的基準，並提供大多數部署很大的緩衝區。

* **公開憑證**:如果您使用自訂網域名稱，您必須購買非 Microsoft 受信任的憑證授權單位所核發的公開憑證。 根據您組織的需求，取得憑證，則可能需要一些時間，並建議開始程序盡越好。 Azure 應用程式 Proxy 支援 standard、[萬用字元](application-proxy-wildcard.md)，或 SAN 為基礎的憑證。

* **網域需求**:單一登入以使用 Kerberos 限制委派 (KCD) 您發佈應用程式需要執行連接器的伺服器與執行應用程式的伺服器是已加入網域且屬於相同的網域或信任網域。
如需本主題的詳細資訊，請參閱[進行單一登入的 KCD](application-proxy-configure-single-sign-on-with-kcd.md)使用應用程式 Proxy。 連接器服務的本機系統內容中執行，並不應該設定為使用自訂身分識別。

* **Url 的 DNS 記錄**

   * 之前使用應用程式 Proxy 中的自訂網域中，您必須建立 CNAME 記錄可讓用戶端的預先定義的應用程式 Proxy 位址解析自訂定義的外部 URL 的公用 DNS 中。 無法建立應用程式使用自訂網域的 CNAME 記錄會防止遠端使用者連線到應用程式。 新增 CNAME 記錄有可能從 DNS 提供者來提供者所需的步驟，了解如何[使用 Azure 入口網站管理 DNS 記錄和記錄集](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)。

   * 同樣地，連接器主機必須能夠解析所發佈之應用程式的內部 URL。

* **系統管理權限和角色**

   * **連接器安裝**需要它要安裝在 Windows server 的本機系統管理員權限。 它也需要最少*應用程式系統管理員*角色驗證和註冊您的 Azure AD 租用戶的連接器執行個體。 

   * **應用程式發佈和管理**需要*應用程式系統管理員*角色。 應用程式系統管理員可以管理所有的應用程式，包括註冊、 SSO 設定、 使用者和群組指派和授權、 應用程式 Proxy 設定，以及同意的目錄中。 它不會授與管理條件式存取的能力。 *雲端應用程式系統管理員*角色具有所有功能的應用程式系統管理員中，不同之處在於它不允許的應用程式 Proxy 設定的管理。

* **授權**：應用程式 Proxy 可透過 Azure AD Basic 的訂用帳戶。 請參閱[Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)如需完整的授權選項和功能清單。  

### <a name="application-discovery"></a>應用程式探索

編譯所有正藉由收集下列資訊以透過應用程式 Proxy 發佈的範圍內應用程式的清查：

| 資訊類型| 若要收集的資訊 |
|---|---|
| 服務類型| 例如: SharePoint、 SAP、 CRM、 自訂的 Web 應用程式、 API |
| 應用程式平台 | 例如: Windows IIS、 Apache 在 Linux、 Tomcat、 NGINX |
| 網域成員資格| 網頁伺服器的完整的網域名稱 (FQDN) |
| 應用程式位置 | Web 伺服器或伺服陣列基礎結構中位於何處 |
| 內部存取 | 在內部存取應用程式時，使用正確的 URL。 <br> 如果伺服陣列中，負載平衡類型正在使用？ <br> 是否在應用程式會從本身以外的來源，繪製內容。<br> 如果應用程式的運作透過 WebSockets 的決定。 |
| 外部存取 | 應用程式已公開給外部廠商解決方案。 <br> 您想要用於外部存取 URL。 如果 SharePoint，請確定每個設定備用存取對應[本指南](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)。 如果沒有，您必須定義的外部 Url。 |
| 公開憑證 | 如果使用自訂網域，取得對應的主體名稱的憑證。 如果憑證存在附註的序號和可以取得它的位置。 |
| 驗證類型| 應用程式支援 Basic、 Windows 整合驗證、 表單型、 標頭為基礎，等宣告所支援的驗證類型。 <br>如果應用程式設定為特定的網域帳戶下執行，請注意完全完整網域名稱 (FQDN) 的服務帳戶。<br> 如果 SAML 為基礎的識別碼和回覆 Url。 <br> 如果標頭為基礎的廠商解決方案和特定的需求，來處理驗證類型。 |
| 連接器群組名稱 | 將會被指定到這個後端應用程式提供的管道和 SSO 的連接器群組的邏輯名稱。 |
| 使用者/群組存取 | 使用者或使用者群組將被授與外部存取應用程式。 |
| 其他需求 | 請注意任何額外的遠端存取或應該納入發行應用程式的安全性需求。 |

您可以下載這[應用程式清查試算表](https://aka.ms/appdiscovery)清查您的應用程式。

### <a name="define-organizational-requirements"></a>當您定義組織的需求

以下是您應該定義您組織的商務需求的區域。 每個區域包含需求的範例

 **Access**

* 遠端使用者使用已加入網域或 Azure AD 已加入裝置使用者可以存取已發佈的應用程式，安全地與無縫單一登入 (SSO)。

* 已核准的個人裝置的遠端使用者可以安全地存取已發佈的應用程式，提供他們在 MFA 中註冊，並已註冊在他們的行動電話上的 Microsoft Authenticator 應用程式做為驗證方法。

**控管** 

* 系統管理員可以定義和監視的 指派使用者給透過應用程式 Proxy 發佈的應用程式生命週期。

**Security**

* 只有使用者指派給應用程式，透過群組成員資格，或個別可以存取這些應用程式。

**效能**

* 沒有相較於存取內部網路的應用程式的應用程式效能降低的情況。

**使用者體驗**

* 使用者並知道如何在任何裝置平台上使用熟悉的公司 Url 來存取他們的應用程式。

**稽核**
* 系統管理員就能夠稽核使用者存取活動。


### <a name="best-practices-for-a-pilot"></a>試驗的最佳作法

決定時間和完全委託與單一登入 (SSO) 的遠端存取的單一應用程式所需的投入時間的量。 藉由執行試驗，將其初始探索、 發行和一般測試這麼做。 使用簡單的 IIS 型 web 應用程式已預先設定的整合式 Windows 驗證 (IWA) 會協助建立基準，此安裝程式需要最省力的成功試驗的遠端存取和單一登入。

下列的設計項目應該會增加您試驗的實作，直接在生產租用戶中的成功。  

**連接器管理**:  

* 連接器會扮演關鍵性的角色，提供您的應用程式的內部部署管道。 使用**預設**連接器群組適合初始試驗的已發行的應用程式之前，先測試到生產環境中委任它們。 成功測試的應用程式可以接著移到生產環境的連接器群組。

**應用程式管理**:

* 您的工作力是最有可能要記住的外部 URL 是熟悉且相關。 避免發佈您的應用程式使用我們預先定義的 msappproxy.net 或 onmicrosoft.com 尾碼。 相反地，提供熟悉最上層已驗證的網域，例如邏輯的主機名稱前置詞*內部網路 < customers_domain > c o m*。

* 限制到試驗群組的試驗應用程式圖示的可見性，藉由隱藏其啟動圖示形式 Azure MyApps 入口網站。 當您準備好開始生產時您可以將範圍給其個別目標對象，在相同的進入生產階段前租用戶，或也發佈在生產租用戶中的 應用程式的應用程式。

**單一登入設定**:某些 SSO 設定有可能需要時間才能完成設定，因此請避免變更控制藉由確保相依性的延遲會事先解決的特定相依性。 這包括網域加入連接器主機來執行 SSO 使用 Kerberos 限制委派 (KCD)，並處理其他耗時的活動。 例如，設定 PING 存取執行個體，如果您需要標頭為基礎的 SSO。

**在連接器主機和目標應用程式之間的 SSL**:因此連接器主機和目標應用程式之間的 TLS 應該一律使用最重要的安全性。 特別是當 web 應用程式有設定表單型驗證 (FBA)，以使用者認證會再有效地以純文字傳輸。

**以累加方式實作和測試每個步驟**。 執行之後發行的應用程式，以確保所有使用者和商務需求，請遵循下列指示都符合基本功能測試：

1. 測試並驗證已停用的預先驗證的 web 應用程式的一般存取。
2. 如果成功啟用預先驗證，並將指派的使用者和群組。 測試並驗證存取。
3. 然後新增您的應用程式的 SSO 方法，並再次測試來驗證存取。
4. 套用條件式存取和所需的 MFA 原則。 測試並驗證存取。

**疑難排解工具**:進行疑難排解時，一律啟動已發佈的應用程式中驗證存取，從連接器主機上的瀏覽器，並確認 應用程式的運作如預期般運作。 更簡單，請在您安裝程式，輕鬆以判斷根本原因，因此請考慮嘗試重現問題的最小的設定，例如使用單一連接器和任何 SSO。 在某些情況下，web 偵錯的 Telerik Fiddler 等工具可以證明在透過 proxy 存取應用程式中存取或內容的問題進行疑難排解不可或缺。 Fiddler 也可以做為 proxy，以協助追蹤和偵錯適用於 iOS 和 Android 等行動裝置平台的流量，而且幾乎任何項目，可以設定要透過 proxy 路由。 請參閱[疑難排解指南](application-proxy-troubleshoot.md)如需詳細資訊。

## <a name="implement-your-solution"></a>實作您的解決方案

### <a name="deploy-application-proxy"></a>部署應用程式 Proxy

部署您的應用程式 Proxy 的步驟所述這[新增遠端存取內部部署應用程式的教學課程](application-proxy-add-on-premises-application.md)。 如果安裝不成功，請選取**疑難排解應用程式 Proxy**在入口網站或使用疑難排解指南[為安裝應用程式 Proxy 代理程式連接器問題](application-proxy-connector-installation-problem.md)。

### <a name="publish-applications-via-application-proxy"></a>發佈應用程式，透過應用程式 Proxy

發行應用程式假設您符合所有必要條件，且您有數個顯示為已註冊的連接器和作用中應用程式 Proxy 頁面。

您也可以使用發佈應用程式[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)。

以下是發佈應用程式時要遵循的一些最佳作法：

* **使用連接器群組**:指派指定給每個個別的應用程式的發行的連接器群組。 我們建議每個連接器群組有兩個以上的連接器，可提供高可用性和延展性。 如果您可能需要以服務的機器，在任何時間點，有三個連接器是最佳作法。 此外，請參閱[個別的網路和使用連接器群組的位置上發佈應用程式](application-proxy-connector-groups.md)以查看您也可以區隔您的網路或位置的連接器使用連接器群組的方式。

* **設定後端應用程式逾時**:此設定是在應用程式，可能需要 75 秒以上才能處理用戶端交易的案例中有用的。 例如當用戶端會將查詢傳送至 web 應用程式，做為資料庫的前端。 前端會將此查詢傳送至其的後端資料庫伺服器，並等候回應，但收到回應時，交談的用戶端逾時。逾時設定為長時間提供較長的交易 180 秒才能完成。

* **使用適當的 Cookie 類型**

   * **僅限 HTTP Cookie**:提供額外的安全性，讓應用程式 Proxy 設定 cookie 的 HTTP 回應標頭中包含 HTTPOnly 旗標。 這項設定有助於減輕例如跨網站指令碼 (XSS) 攻擊。 將此設定為否需要存取工作階段 cookie 的用戶端/使用者代理程式。 比方說，連線到遠端桌面閘道的 RDP/MTSC 用戶端會透過應用程式 Proxy 發佈。

   * **安全 Cookie**:當 cookie 設定使用安全的屬性時，使用者代理程式 （用戶端應用程式） 將只包含 cookie 在 HTTP 要求中如果要求透過 TLS 安全通道傳輸。 這有助於降低遭入侵純文字通道，因此應該啟用 cookie 的風險。

   * **永續性 Cookie**:可讓應用程式 Proxy 工作階段 cookie，由仍然有效，直到它到期或已刪除的瀏覽器封閉區段之間保存。 使用案例是豐富的應用程式，例如 office 存取已發佈的 web 應用程式內的文件不會重新提示使用者進行驗證的位置。 謹慎啟用不過，為持續性 cookie 最終導致服務風險的未經授權的存取，如果沒有其他補償控制項搭配使用。 此設定僅用於無法共用處理序之間的 cookie 的舊版應用程式。 最好是以更新您的應用程式處理而不是使用這項設定的處理序間共用 cookie。

* **轉譯標頭中的 Url**:您可以啟用此功能，您無法將內部 DNS 設定為符合組織的公用命名空間 （也稱為分割 DNS） 的案例。 除非您的應用程式需要用戶端要求中的原始主機標頭，將保留此值設定為 [是]。 替代方式是讓連接器使用內部 url 的 FQDN，進行路由的實際流量，且外部 URL，做為主機標頭中的 FQDN。 在大部分情況下此替代方案應該允許應用程式運作正常，從遠端存取時，但您的使用者無法擁有比對，內部和外部 URL 的優點。

* **轉譯應用程式主體中的 URL**：當您想要傳回給用戶端的回應中轉譯該應用程式的連結時，請開啟應用程式的應用程式內文連結轉譯。 如果啟用，則此函式會提供嘗試在轉譯應用程式 Proxy 會尋找在 HTML 和 CSS 的回應傳回給用戶端中的所有內部連結。 發行包含硬式編碼絕對或 NetBIOS 簡短名稱連結，在內容中，應用程式或應用程式連結到其他的內容與內部部署應用程式時，它是很有用。

連結到另一個已發佈應用程式發佈的應用程式的情況下，請啟用連結轉譯，每個應用程式，以便您可以在每個應用程式層級的使用者體驗的控制。

例如，假設您有三個透過 Application Proxy 發佈且彼此連結的應用程式：優點、 費用，並旅行，以及第四個應用程式，不會透過應用程式 Proxy 發佈的意見。

![圖 1](media/App-proxy-deployment-plan/link-translation.png)

當您啟用 Benefits 應用程式的連結轉譯時，Expenses 和 Travel 的連結重新導向至應用程式，所需的外部 Url，讓存取的應用程式從公司網路外部的使用者可以存取它們。 從 Expenses 和 Travel 回到 Benefits 的連結不適用，因為這些兩個應用程式尚未啟用連結轉譯。 因為沒有外部 URL，因此使用 Benefits 應用程式的使用者無法存取來自公司網路外部的意見反應應用程式，並未重新導向 Feedback 的連結。 請參閱詳細的資訊[翻譯和其他重新導向選項連結](application-proxy-configure-hard-coded-link-translation.md)。

### <a name="access-your-application"></a>存取您的應用程式

有多種方法，來管理存取權給應用程式 Proxy 發佈的資源，因此請選擇最適合您特定的案例和延展性需求。 常見的方法包括： 使用透過 Azure AD Connect 正在同步處理內部部署群組，請在 Azure AD 中建立動態群組根據使用者屬性，使用自助式群組管理的資源擁有者或所有這些的組合。 請參閱連結的資源，如各自的優點。

指派使用者存取應用程式的最直接的方式進入**使用者和群組**發佈的應用程式和直接指派的群組或個人的左側窗格中的選項。

![圖 24](media/App-proxy-deployment-plan/add-user.png)

您也可以允許使用者自助服務存取您的應用程式，指派目前不隸屬的群組，並設定自我提供的選項。

![圖 25](media/App-proxy-deployment-plan/allow-access.png)

如果啟用，則是使用者能夠登入 MyApps 入口網站並要求存取權，且其中一個自動核准，並從指定的核准者新增至已允許自助服務群組或必須經過核准。

來賓使用者也可以[邀請，存取透過 Azure AD B2B 的應用程式 Proxy 發佈內部應用程式](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)。

針對內部部署應用程式，通常可存取以匿名方式，需要進行任何驗證，您可能會想要停用此選項位於 應用程式的**屬性**。

![圖 26](media/App-proxy-deployment-plan/assignment-required.png)


離開此選項設定為 [否] 可讓使用者能夠存取內部部署應用程式透過 Azure AD App Proxy 不具權限，因此請謹慎使用。

您的應用程式發行之後，它應該是可存取輸入瀏覽器中的外部 URL，或在其圖示[ https://myapps.microsoft.com ](https://myapps.microsoft.com/)。

### <a name="enable-pre-authentication"></a>啟用預先驗證

請確認您的應用程式可透過存取透過外部 URL 的應用程式 Proxy 存取。 

1. 瀏覽至 [Azure Active Directory]   > [企業應用程式]   > [所有應用程式]  ，然後選擇您要管理的應用程式。

2. 選取 [應用程式 Proxy]  。

3. 在 **預先驗證**欄位中，使用下拉式清單中選取**Azure Active Directory**，然後選取**儲存**。

啟用預先驗證，Azure AD 會先挑戰的使用者，進行驗證，如果單一登入是 configued 然後後端應用程式也會驗證使用者才能存取應用程式。 從傳遞的預先驗證模式變更為 「 Azure AD 也會設定外部 URL 使用 HTTPS，因此一開始設定為 HTTP 的任何應用程式現在將使用 HTTPS 來保護。

### <a name="enable-single-sign-on"></a>啟用單一登入

因為使用者只需要登入一次存取 Azure AD 時，SSO 便會提供最佳的可能的使用者體驗和安全性。 預先驗證使用者之後, 由應用程式 Proxy 連接器驗證內部部署應用程式，代表使用者執行 SSO。 後端應用程式會處理登入，如同它是使用者本身。 

選擇**通過**選項可讓使用者存取已發行的應用程式，而不需要對 Azure AD 進行驗證。

執行 SSO 才可能如果 Azure AD 可以識別要求存取資源，因此您的應用程式必須設定為預先驗證 SSO 的存取時的 Azure ad 的使用者函式的使用者，否則 SSO 選項將會停用。

讀取[單一登入 Azure AD 中的應用程式](what-is-single-sign-on.md)可協助您設定您的應用程式時，請選擇最適合的 SSO 方法。

###  <a name="working-with-other-types-of-applications"></a>使用其他類型的應用程式

Azure AD Application Proxy 也可以支援為了使用我們的 Azure AD Authentication Library 所開發的應用程式 ([ADAL](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)) 或 Microsoft Authentication Library ([MSAL](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/))。 它可以支援原生用戶端應用程式所使用的 Azure AD 發行執行代表使用者的預先驗證的用戶端要求的標頭資訊中所收到的權杖。

讀取[發佈原生和行動用戶端應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client)並[宣告式應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps)若要了解可用的應用程式 Proxy 的組態。

### <a name="use-conditional-access-to-strengthen-security"></a>若要加強安全性中使用條件式存取

應用程式的安全性需要一組進階的安全性功能，可以防止並回應複雜的威脅與內部部署和雲端中。 攻擊者最常存取公司網路透過弱式、 預設或竊取的使用者認證。  Microsoft 身分識別導向安全性會使用遭竊的認證來減少管理及保護特殊權限和非特殊權限的身分識別。

可用來支援 Azure AD 應用程式 Proxy 設定了下列功能：

* 使用者和位置型條件式存取：藉由限制根據地理位置或 IP 位址與使用者存取受保護的機密資料[位置型條件式存取原則](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。

* 裝置型條件式存取：確保只有已註冊、 已核准，且符合規範的裝置可以存取公司資料[裝置型條件式存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)。

* 應用程式型條件式存取：工作不需要使用者不在公司網路上時停止。 [安全存取公司的雲端和內部部署應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)和維護使用條件式存取的控制項。

* 風險型條件式存取：保護資料免於惡意駭客[風險型條件式存取原則](https://www.microsoft.com/cloud-platform/conditional-access)，可以套用至所有的應用程式和所有使用者，是否在內部部署或雲端中。

* Azure AD 存取面板：與您的應用程式 Proxy 服務部署，安全地發佈的應用程式，為使用者提供簡單的中樞，以探索及存取其所有的應用程式。 使用自助功能，例如要求存取新的應用程式和群組，或透過管理這些資源的存取權，代表其他人，能夠提升生產力[存取面板](https://aka.ms/AccessPanelDPDownload)。

## <a name="manage-your-implementation"></a>管理您的實作

### <a name="required-roles"></a>必要的角色

Microsoft 是代表授與執行所需的工作，與 Azure AD 的最低可能權限的原則。 [檢閱可用的不同 Azure 角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)選擇正確的檔案，以解決每個角色的需求。 有些角色可能需要暫時套用和部署完成之後移除。

| 商務角色| 商業工作| Azure AD 角色 |
|---|---|---|
| 協助服務中心系統管理員 | 通常僅限於合格的使用者報告問題並執行有限的工作，例如變更使用者的密碼，讓重新整理權杖失效和監視服務健康情況。 | 服務台系統管理員 |
| 身分識別管理| 讀取 Azure AD 登入報告和稽核記錄檔偵錯應用程式 Proxy 的相關問題。| 安全性讀取者 |
| 應用程式擁有者| 建立和管理企業應用程式、 應用程式註冊和應用程式 proxy 設定的所有層面。| 應用程式系統管理員 |
| 基礎結構系統管理員 | 憑證變換的擁有者 | 應用程式系統管理員 |

少量的安全資訊或資源的存取權的人將有助於減少惡意的動作項目取得未經授權的存取權，或者授權的使用者無意中影響到敏感資源的機會。 
 
不過，使用者仍然需要執行特殊權限的日常作業，因此強制執行在 just-in-time (JIT) 基礎[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)原則，以提供隨特殊權限的 Azure 資源的存取權，以及 Azure AD 是我們建議的手法有效地管理系統管理存取權，以及稽核。

### <a name="reporting-and-monitoring"></a>報告和監控

Azure AD 可以提供額外的見解，在貴組織的使用者佈建使用方式和透過稽核記錄和報告的操作健全狀況。 

#### <a name="application-audit-logs"></a>應用程式稽核記錄

這些記錄檔會提供有關設定應用程式 Proxy 和裝置與使用者存取應用程式的應用程式的登入的詳細的資訊。 稽核記錄位於稽核 API 和 Azure 入口網站中匯出。

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 事件記錄檔和效能計數器

連接器有系統管理員和工作階段記錄檔。 系統管理記錄包含索引鍵事件和其錯誤。 這些工作階段記錄包含所有交易，以及它們的處理詳細資料。 記錄檔和計數器位於在 「 Windows 事件記錄檔中，並遵循這[教學課程，以在 Azure 監視器中設定事件記錄檔資料來源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)。

### <a name="troubleshooting-guide-and-steps"></a>疑難排解指南和步驟

深入了解常見的問題和解決方式與我們的指南，來[疑難排解](application-proxy-troubleshoot.md)錯誤訊息。 

下列文章會說明常見的案例也可以用來建立支援組織的疑難排解指引。 

* [顯示應用程式頁面時發生問題](application-proxy-page-appearance-broken-problem.md)
* [應用程式載入時間過長](application-proxy-page-load-speed-problem.md)
* [應用程式頁面連結無效](application-proxy-page-links-broken-problem.md)
* [要開啟哪些適用於應用程式的連接埠](application-proxy-connectivity-ports-how-to.md)
* [適用於應用程式的連接器群組中沒有運作中的連接器](application-proxy-connectivity-no-working-connector.md)
* [在管理入口網站中設定](application-proxy-config-how-to.md)
* [在應用程式中設定單一登入](application-proxy-config-sso-how-to.md)
* [在管理入口網站中建立應用程式時發生問題](application-proxy-config-problem.md)
* [設定 Kerberos 限制委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [使用 PingAccess 設定](application-proxy-back-end-ping-access-how-to.md)
* [無法存取此公司應用程式錯誤](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [安裝應用程式 Proxy 代理程式連接器時發生問題](application-proxy-connector-installation-problem.md)
* [登入問題](application-sign-in-problem-on-premises-application-proxy.md)
