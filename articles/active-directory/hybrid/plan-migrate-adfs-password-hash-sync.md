---
title: Azure AD Connect：從同盟移轉至 Azure AD 的密碼雜湊同步處理 | Microsoft Docs
description: 將混合式身分識別環境從同盟移至密碼雜湊同步處理的相關資訊。
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cad368cb968b94d1327cc99ed4dfa6df0aedd2cd
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555093"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>從同盟移轉至 Azure AD 的密碼雜湊同步處理
下列文件提供從 AD FS 移至密碼雜湊同步處理的相關指引。

>[!NOTE]
>您可以在[此處](https://aka.ms/ADFSTOPHSDPDownload)下載本文件複本。


## <a name="prerequisites-for-the-migration"></a>移轉的必要條件 
移轉之前必須符合下列先決條件。
### <a name="update-azure-ad-connect"></a>更新 Azure AD Connect

若要成功執行移轉到傳遞驗證的步驟，至少應有 [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0。 此版本包含執行登入轉換方式的重大變更，而且可將從同盟移轉到雲端驗證的整體時間從數小時縮減至數分鐘。

> [!IMPORTANT]
> 過時的文件、工具和部落格文章上，註明了當從同盟網域轉換成受控網域時，使用者轉換是必要的步驟。 請注意，現在不再需要轉換使用者，而且 Microsoft 正在努力更新說明文件和工具來反映此項變更。

若要將 Azure AD Connect 更新至最新版本，請依照本[更新指示](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)操作。

### <a name="password-hash-synchronization-required-permissions"></a>密碼雜湊同步處理所需的權限

Azure AD Connect 可使用「快速設定」或「自訂安裝」來設定。 如果使用 [自訂安裝] 選項，可能會沒有密碼雜湊同步處理的[必要權限](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions)。

Azure AD Connect AD DS 服務帳戶需要下列權限才能同步處理密碼雜湊。

* 複寫目錄變更

* 複寫目錄變更 (全部)

現在正是時候來驗證這些權限是否已準備好供樹系中所有網域使用。

### <a name="plan-migration-method"></a>規劃移轉方法

有兩種方法可以從同盟驗證移轉至密碼雜湊同步處理和無縫 SSO。 您使用的方法將取決於原本設定 AD FS 的方式。 



- **選項 A：使用 Azure AD Connect**。 如果原本是使用 Azure AD Connect 設定 AD FS，則必須透過 Azure AD Connect 精靈來變更做為使用者登入方式使用的密碼雜湊同步處理。   
使用 Azure AD Connect 時，若要變更使用者登入方式，就會自動幫您執行 Set-MsolDomainAuthentication Cmdlet，因此您會無法控制 Azure AD Connect 將您 Azure 租用戶中所有經過驗證的同盟網域解除同盟。

> [!NOTE]
> 此時，如果原先是使用 AAD Connect 設定 AD FS，將使用者登入方式變更成密碼雜湊同步處理時，就無法避免會將租用戶中所有網域解除同盟。  



- **選項 B：使用 Azure AD Connect 和 PowerShell**。 只有在原先並非以 Azure AD Connect 設定 AD FS 的情況下，才可使用此方法。 您仍需要透過 Azure AD Connect 精靈變更使用者登入方法，但主要差異在於此方式不會自動執行 Set-MsolDomainAuthentication Cmdlet，因為此方式不知道您的 AD FS 伺服器陣列，所以您可以完全控制要轉換的網域和順序。

若要了解您應該使用何種方式，請執行下一節中的步驟。

#### <a name="verify-current-user-sign-in-settings"></a>請確認目前的使用者登入設定

使用全域系統管理員帳戶登入 Azure AD 入口網站[https://aad.portal.azure.com](https://aad.portal.azure.com/)，確認您目前的使用者登入設定。

在 [使用者登入] 區段中，確認 [同盟] 設為 [已啟用]，而 [無縫單一登入] 和 [傳遞驗證] 設為 [已停用]。 也請確認密碼同步處理的狀態應顯示為 [已停用]，除非先前已啟用。

![圖 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>確認 Azure AD Connect 設定

   1. 前往您的 Azure AD Connect 伺服器並啟動 Azure AD Connect，然後選取 [設定]。 
   2. 在 [其他工作] 畫面中，選取 [檢視目前的設定]，然後選取 [下一步]。</br>
   ![圖 31](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. 在 [Review Your Solution]\(檢視您的解決方案\) 畫面中，記下密碼同步處理的狀態。</br> 

   如果密碼雜湊同步處理目前設定為 [已停用]，請務必按照本指南的步驟將之啟用。 如果密碼雜湊同步處理目前設定為 [已啟用]，您可以放心地略過本指南的[步驟 1 – 啟用密碼雜湊同步處理](#step-1--enable-password-hash-synchronization)一節。
   4. 在 [Review Your Solution]\(檢視您的解決方案\) 畫面中，向下捲動至 [Active Directory Federation Services (AD FS)]。</br>
 
   如果您在此區段看到 AD FS 設定，可以安心認定原先是透過 Azure AD Connect 設定 AD FS，因此可以透過 Azure AD Connect 的 [變更使用者登入] 選項將同盟網域轉換至受控網域；本節的**選項 A：使用 Azure AD Connect 從同盟轉換至密碼雜湊同步處理**會詳述此程序。
   5. 如果看不到目前設定中所列的 Active Directory 同盟服務，則必須透過 PowerShell 將同盟網域手動轉換至受控網域，此程序會在**選項 B - 使用 Azure AD Connect 和 PowerShell 從同盟轉換至密碼雜湊同步處理**一節中詳述。

### <a name="document-current-federation-settings"></a>文件目前的同盟設定

您可以執行 Get-MsolDomainFederationSettings Cmdlet 來尋找目前的同盟設定。

此命令為：

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

例如︰

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```
驗證可能已針對同盟設計和部署說明文件所自訂的任何設定，尤其是 PreferredAuthenticationProtocol、SupportsMfa 及 PromptLoginBehavior。

以下提供這些設定相關功能的詳細資訊。

[Active Directory 同盟服務提示=登入參數支援](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> 如果 SupportsMfa 值目前設為「True」，表示您使用的是內部部署的 MFA 解決方案，會將第二個因素挑戰納入使用者驗證流程中。 如此就不再適用於 Azure AD 的驗證情節，而您必須改用 Azure MFA (雲端式) 服務來執行相同的函式。 繼續進行之前，請先仔細評估您的 MFA 需求，並確定您了解如何運用 Azure MFA、授權意涵以及轉換網域之前的一般使用者註冊程序。 [https://aka.ms/deploymentplans](https://aka.ms/deploymentplans) 有提供更加詳盡的 Azure MFA 部署指南。

#### <a name="backup-federation-settings"></a>備份同盟設定

雖然在此程序期間不會變更 AD FS 伺服器陣列上的其他信賴憑證者，但建議先確定您的 AD FS 伺服器陣列目前擁有有效備份可供還原。 您可以使用免費的 Microsoft [AD FS 快速還原工具](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)來還原。 這項工具可將 AD FS 備份和還原到現有的伺服陣列或新的伺服器陣列。

如果您選擇不使用 AD FS 快速還原工具，至少應匯出「Microsoft Office 365 身分識別平台」信賴憑證者信任，以及您新增的任何相關聯自訂宣告規則。 您可以透過下列 PowerShell 範例執行這項作業

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>部署考量和 AD FS 使用方式

### <a name="validate-your-current-ad-fs-usage"></a>驗證目前的 AD FS 使用方式

從同盟轉換成受控之前，應該仔細查看您目前針對 Azure AD/Office 365 和其他應用程式 (信賴憑證者信任) 使用 AD FS 的方式。 尤其應考量下列表格中的內容：

| 假設情形| 對應行動 |
|-|-|
| 您要保留 AD FS 供其他應用程式使用。| 您會同時使用 AD FS 與 Azure AD，因而需要考慮一般使用者體驗。 在某些情況下，使用者可能需要進行兩次驗證，一次是 Azure AD (會在此將 SSO 向外轉發到 Office 365 等其他應用程式)，另一次則是為了任何仍繫結至 AD FS 作為信賴憑證者信任的應用程式。 |
| AD FS 是高度自訂且仰賴 onload.js 檔案中特定的自訂設定，而 onload.js 檔案無法在 Azure AD 中複製 (例如您已變更登入體驗，所以使用者只能輸入 SamAccountName 格式的使用者名稱，而不是 UPN，或是會有高度品牌化的登入體驗)| 您必須確認 Azure AD 可以滿足您目前的自訂需求後再繼續。 如需詳細資訊和指引，請參閱 AD FS 商標和 AD FS 自訂區段。|
| 您正透過 AD FS 封鎖舊版驗證用戶端。| 請考慮以[舊版驗證的條件式存取控制](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)和 [Exchange Online 用戶端存取規則](http://aka.ms/EXOCAR)來更換控制項，以封鎖目前在 AD FS 上的舊版驗證用戶端。|
| 您要求使用者向 AD FS 進行驗證時，對內部部署 MFA 伺服器解決方案執行 MFA。| 您無法透過內部部署 MFA 解決方案將 MFA 挑戰納入受控網域的驗證流程，但是可以在網域轉換後使用 Azure MFA 服務繼續執行此操作。 如果使用者目前並未使用 Azure MFA，就會需要一次性的使用者註冊步驟，您必須對此做好準備並告知給使用者。|
| 您目前使用 AD FS 中的存取控制原則 (AuthZ 規則) 來控管 Office 365 的存取權。| 請考慮將這些更換成對等的 Azure AD [條件式存取原則](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)和 [Exchange Online 用戶端存取規則](http://aka.ms/EXOCAR)。|

### <a name="considerations-for-common-ad-fs-customizations"></a>常見的 AD FS 自訂考量

#### <a name="inside-corporate-network-claim"></a>公司網路內部宣告

如果使用者在公司網路內部進行驗證，就會由 AD FS 發出 InsideCorporateNetwork 宣告。 此宣告可以傳遞至 Azure AD，並根據使用者的網路位置用來略過多重要素驗證。 如需進一步了解如何判斷目前是否在 AD FS 中啟用此功能，請參閱[同盟使用者適用的可信任 IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)。

一旦您的網域都轉換成密碼雜湊同步處理，便再也無法使用 InsideCorporateNetwork 宣告。 可使用 [Azure AD 中的具名位置](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)來取代這項功能。

一旦設定了具名位置，所有已設定要包含/排除網路位置「所有可信任位置」和「MFA 可信任 IP」的條件式存取原則都必須更新，以反映新建立的具名位置。

如需條件式存取中位置條件的詳細資訊，請參閱 [Active Directory 條件式存取位置](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。

#### <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

將裝置加入 Azure AD 即可讓您建立條件式存取規則，強制裝置達到您的安全性與合規性存取標準，而讓使用者得以使用公司工作帳戶或學校帳戶登入裝置，而不是用個人帳號登入。 混合式 Azure AD 已加入裝置可讓您將已加入 AD 網域的裝置加入 Azure AD。 可能已使用此功能設定同盟環境。

為了確保網域轉換至密碼雜湊同步處理後，Hybrid Join 仍然適用於加入此網域的任何新裝置，必須將 Azure AD Connect 設定為同步 Windows 10 適用的 Active Directory 電腦帳戶和 Azure AD。 若是 Windows 7 和 Windows 8 的電腦帳戶，混合式加入會使用無縫 SSO 註冊 Azure AD 中的電腦，您就不必像 Windows 10 裝置那樣同步處理這些帳戶。 但是您必須將更新的 workplacejoin.exe 檔案 (透過 .msi) 部署到這些舊版用戶端，讓他們可以使用無縫 SSO 自行註冊。 [下載 .msi](https://www.microsoft.com/download/details.aspx?id=53554)。 

如需詳細資訊，請參閱[如何設定已加入 Azure Active Directory 的混合式裝置](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)。

#### <a name="branding"></a>商標

您的組織可能會有[自訂的 ADFS 登入頁面](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)來顯示更多有關組織的資訊。 若是如此，可考慮製作相似的[自訂 Azure AD 登入頁面](https://docs.microsoft.com/azure/active-directory/customize-branding)。

使用類似的自訂時，外觀上應該會有些許改變。 建議在與一般使用者的通訊中加入預期內的改變。

> [!NOTE]
> 公司商標只有在您已購買 Azure AD 的「進階」或「基本」授權，或擁有 Office 365 授權時，才可供使用。

## <a name="planning-deployment-and-support"></a>規劃部署和支援

### <a name="plan-the-maintenance-window"></a>規劃維護時間範圍

當網域轉換程序本身相對快速時，在網域轉換完成後，Azure AD 可能仍會將部分驗證要求傳送到您的 AD FS 伺服器，時間最長可達 4 小時。 在這四小時期間內，視不同的服務端快取而定，Azure AD 可能不會接受驗證，而且使用者會收到錯誤，因為他們依然能夠成功地向 AD FS 進行驗證，但由於現在已移除該同盟信任，Azure AD 不會再接受使用者發出的權杖。

> [!NOTE]
> 這只會影響在這段後轉換期間，透過瀏覽器存取服務的使用者，直到清除服務端快取為止。 舊版用戶端 (Exchange ActiveSync、Outlook 2010/2013) 應該不會受到影響，因為 Exchange Online 會將其認證中的快取保留一段時間，以無訊息方式重新驗證使用者，不需要返回到 AD FS。 一旦清除此快取後，這些用戶端儲存在裝置上的認證就能以無訊息方式重新驗證用戶端，因此使用者應該不會因為網域轉換程序而收到任何密碼提示。 相反地，若是新式驗證用戶端 (Office 2013/2016、IOS 及 Android 應用程式)，則會使用有效的重新整理權杖取得新的存取權杖，以便繼續存取資源，而不必返回 AD FS，因此在網域轉換程序中不會收到任何密碼提示，而且不需要任何額外的設定就能繼續運作。

> [!IMPORTANT]
> 請勿關閉您的 AD FS 環境或移除 Office 365 信賴憑證者信任，除非您已經使用雲端驗證確認所有使用者都已驗證成功。

### <a name="plan-for-rollback"></a>規劃復原作業

如果找到主要問題而且無法快速解決，您可能需決定將解決方案復原至同盟。 請務必規劃無法如預期部署時的因應措施。 如果部署時網域或使用者轉換失敗，或是需要復原至同盟，請務必要知道如何減輕任何中斷情況，並降低對使用者的影響。

#### <a name="rolling-back"></a>復原

如需特定的部署詳細資料，請參閱您的同盟設計和部署文件。 此流程應該包含：

* 使用 Convert-MSOLDomainToFederated 將受控網域轉換成同盟網域 

* 必要時設定其他宣告規則。

### <a name="plan-change-communications"></a>規劃變更消息通知

規劃部署和支援的其中一項要點是，務必主動向一般使用者通知所做的變更以及可能經歷或必須執行的內容。 

部署密碼雜湊同步處理和無縫 SSO 之後，存取 Office 365 以及透過 Azure AD 驗證的其他相關聯資源時，會變更使用者登入體驗。 外部網路的使用者現在只會看到 Azure AD 登入頁面，而不是重新導向至外部 Web 應用程式 Proxy 伺服器所呈現的表單式頁面。

通知的策略規劃包含多項要素。 其中包含：

* 透過以下方式通知使用者即將推出和已發行的功能
  * 電子郵件和其他內部通訊管道
  * 海報等視覺物件
  * 執行即時或其他通訊
* 決定自訂人員、傳送通訊人員以及時間。

## <a name="implementing-your-solution"></a>實作您的解決方案

既已規劃了解決方案，便準備進一步實作。 實作包含下列要素：

1. 啟用密碼雜湊同步處理

2. 準備無縫單一登入

3. 將登入方法變更成密碼雜湊同步處理，並啟用無縫 SSO

### <a name="step-1--enable-password-hash-synchronization"></a>步驟 1 – 啟用密碼雜湊同步處理

實作此解決方案的第一個步驟，便是在 Azure AD Connect 精靈中啟用密碼雜湊同步處理。 密碼雜湊同步處理是選用功能，可以在使用同盟的環境中啟用，而不會對驗證流程產生任何影響。 在此情況下，Azure AD Connect 將會開始同步處理密碼雜湊，而不影響使用同盟的使用者登入。

因此，建議在變更網域登入方法之前，請先執行此步驟作為準備工作。 如此可讓您有充裕的時間驗證密碼雜湊同步處理是否能正常運作。

啟用密碼雜湊同步處理：

   1. 在 Azure AD Connect 伺服器上開啟精靈，然後選取 [設定]。
   2. 選取 [自訂同步處理選項]，然後選取 [下一步]。
   3. 在 [Connect to Azure AD] \(連線到 Azure AD\) 畫面中，提供全域系統管理員的使用者名稱和密碼。
   4. 在 [Connect your directories] \(連線您的目錄\) 畫面中，按一下 [下一步]。
   5. 在 [Domain and OU filtering] \(網域與 OU 篩選\)畫面中，按一下 [下一步]。
   6. 在 [選用功能] 畫面中選取 [密碼同步處理]，然後選取 [下一步]。
   ![圖 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. 在所有其餘的畫面中選取 [下一步]，然後在最後一個畫面選取 [設定]。
   8. Azure AD Connect 將會在下次同步時，開始同步處理密碼雜湊。

一旦啟用密碼雜湊同步處理，Azure AD Connect 同步處理範圍中所有使用者的密碼雜湊將會重新雜湊並寫入 Azure AD。 根據使用者數量，這項作業可能需要數分鐘到數小時。

若是用於規劃目的，請估計 1 小時內大約可處理 20,000 名使用者。

若要驗證密碼雜湊同步處理可正確運作，請使用 Azure AD Connect 精靈中的疑難排解工作。

   1. 在您的 Azure AD Connect 伺服器上，使用 [以系統管理員身分執行] 選項開啟新的 Windows PowerShell 工作階段。
   2. 執行 Set-ExecutionPolicy RemoteSigned 或 Set-ExecutionPolicy Unrestricted.
   3. 啟動 Azure AD Connect 精靈。
   4. 瀏覽至 [其他工作] 頁面，選取 [疑難排解]，然後按 [下一步]。
   5. 在 [疑難排解] 頁面上，按一下 [啟動]，以在 PowerShell 中啟動疑難排解功能表。
   6. 在主功能表中，選取 [Troubleshoot password hash synchronization]\(疑難排解密碼雜湊同步處理的問題\)。
   7. 在子功能表中，選取 [Password hash synchronization does not work]\(密碼雜湊同步處理完全無法運作\)。

如果您發現問題，請使用[本文章](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)的資訊進行疑難排解。

### <a name="step-2--prepare-for-seamless-sso"></a>步驟 2 - 準備無縫 SSO

若是您的裝置要使用無縫 SSO，則必須使用 Active Directory 中的群組原則，將 Azure AD URL 新增至使用者的內部網路區域設定。

瀏覽器預設會自動從指定的 URL 計算正確的區域 (網際網路或內部網路)。 例如，"http://contoso/" 會對應到內部網路區域，而 "http://intranet.contoso.com/" 則會對應到網際網路區域 (因為 URL 包含句點)。 除非將 URL 明確地新增至瀏覽器的內部網路區域，否則瀏覽器不會將 Kerberos 票證傳送給雲端端點 (例如 Azure AD URL)。

請依照[步驟將必要變更](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)套用到您的裝置。

> [!IMPORTANT]
> 執行這項變更不會修改使用者登入 Azure AD 的方式。 但是，繼續執行步驟 3 之前請將此設定套用到所有裝置，這點非常重要。 另外請注意，使用者登入未收到這項設定的裝置時，只需輸入使用者名稱和密碼即可登入 Azure AD。

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>步驟 3 – 將登入方法變更為 PHS 並啟用無縫 SSO

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>選項 A - 使用 Azure AD Connect 從同盟轉換至 PHS

如果最初使用 Azure AD Connect 設定 AD FS，請使用此方法。 如果最初並非使用 Azure AD Connect 設定 AD FS，就無法使用此方法。 先**變更使用者登入方法**

   1. 在 Azure AD Connect 伺服器上開啟精靈。
   2. 選取 [變更使用者登入]，然後選取 [下一步]。
   ![圖 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. 在 [Connect to Azure AD] \(連線到 Azure AD\) 畫面中，提供**全域系統管理員**的使用者名稱和密碼。
   4. 在 [使用者登入] 畫面中，將選項按鈕從 [與 AD FS 同盟] 變更成 [密碼雜湊同步處理]，並且請務必勾選此方塊。請勿轉換使用者帳戶，此步驟已遭到取代，且日後會從 AAD Connect 版本中移除。 也請選取 [啟用單一登入]，然後選取 [下一步]。
   ![圖 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > 開始使用 Azure AD Connect 版本 1.1.880.0，在預設情況下會啟用 [無縫單一登入] 核取方塊。
   
   > [!IMPORTANT]
   > 您可以放心地忽略警告；這些警告是用於說明使用者轉換和完整的密碼雜湊同步處理均是從同盟轉換成雲端驗證時所需的步驟。 請注意，已不再需要執行這些步驟，日後的 Azure AD Connect 版本不會提供轉換使用者的選項。 如果仍會看到這些警告，請確認您執行的是否是最新版的 Azure AD Connect，以及是否在使用本指南的最新版本。 如需詳細資訊，請參閱[更新 Azure AD Connect](#_Update_Azure_AD) 一節。
   
   5. 在 [啟用單一登入] 畫面中，輸入網域系統管理員帳戶的認證，然後選取 [下一步]。
   ![圖 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > 需要網域系統管理員認證才能啟用無縫單一登入，因為此程序會執行下列這些需要較高權限的動作。 網域管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中。 這些只能用來啟用此功能，而且成功完成後即捨棄
   >  * 名為 AZUREADSSOACC 的電腦帳戶 (代表 Azure AD) 是在您的內部部署 Active Directory (AD) 中建立。
   >  * 電腦帳戶的 Kerberos 解密金鑰可安全地與 Azure AD 共用。
   >  * 此外，系統會建立兩個 Kerberos 服務主體名稱 (SPN)，以代表 Azure AD 登入期間所使用的兩個 URL。
   >  * 網域管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中。 這些只能用來啟用此功能，而且成功完成後即捨棄
   
   6. 在 [Ready to Configure]\(準備進行設定\) 畫面上，請確定已勾選 [Start Synchronization process when configuration completes]\(設定完成時啟動同步處理流程\) 此核取方塊。 然後選取 [設定]。
   ![圖 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > 此時您所有的同盟網域都會變更為受控驗證，現在會使用密碼雜湊同步處理作為驗證的方法。
       
   7. 開啟 [Azure AD 入口網站]、選取 [Azure Active Directory]，然後選取 [Azure AD Connect]。
   8. 確認同盟 [已停用]，同時 [無縫單一登入] 和 [密碼同步處理] 設為 [已啟用]。  
  ![圖 37](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. 請前往[測試和下一個步驟](#testing-and-next-steps)。
   
   > [!IMPORTANT]
   > 略過「選項 B - 使用 Azure AD Connect 和 PowerShell 從同盟轉換至 密碼雜湊同步處理」一節，因為該節中的步驟不適用。  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>選項 B - 使用 Azure AD Connect 和 PowerShell 從同盟轉換至 PHS

如果您的同盟一開始不是使用 Azure AD Connect 設定，請使用此選項。

您將在此程序中啟用 [無縫式 SSO]，並且從同盟網域轉換至受控網域。

   1. 在 Azure AD Connect 伺服器上開啟精靈。
   2. 選取 [變更使用者登入]，然後選取 [下一步]。 
   3. 在 [Connect to Azure AD] \(連線到 Azure AD\) 畫面中，提供全域系統管理員的使用者名稱和密碼。
   4. 在 [使用者登入] 畫面中，將選項按鈕從 [不設定] 變更成 [密碼雜湊同步處理]、選取 [啟用單一登入]，然後選取 [下一步]。
   
   變更前：![圖 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   變更後：  
   ![圖 22](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > 開始使用 Azure AD Connect 版本 1.1.880.0，在預設情況下會啟用 [無縫單一登入] 核取方塊。
   
   5. 在 [啟用單一登入] 畫面中，輸入網域系統管理員帳戶的認證，然後選取 [下一步]。
   
   > [!NOTE]
   > 需要網域系統管理員認證才能啟用無縫單一登入，因為此程序會執行下列這些需要較高權限的動作。 網域管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中。 這些只能用來啟用此功能，而且成功完成後即捨棄。
   > * 名為 AZUREADSSOACC 的電腦帳戶 (代表 Azure AD) 是在您的內部部署 Active Directory (AD) 中建立。
   > * 電腦帳戶的 Kerberos 解密金鑰可安全地與 Azure AD 共用。
   > * 此外，系統會建立兩個 Kerberos 服務主體名稱 (SPN)，以代表 Azure AD 登入期間所使用的兩個 URL。
   
   6. 在 [Ready to Configure]\(準備進行設定\) 畫面上，請確定已勾選 [Start Synchronization process when configuration completes]\(設定完成時啟動同步處理流程\) 此核取方塊。 然後選取 [設定]。
   ![圖 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   選取 [設定] 時，會按照預覽步驟設定無縫 SSO。 不會修改密碼雜湊同步處理設定，因為之前已經啟用此功能。
   
   > [!IMPORTANT]
   > 此時不會變更使用者的登入方式。  
   
   7. 在 Azure AD 入口網站中，請確認 [同盟] 持續設為 [已啟用]，而且 [無縫單一登入] 現在也設為 [已啟用]。
   ![圖 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>從同盟網域轉換成受控網域

此時，同盟仍是已啟用狀態，而且可供您的網域運作。 若要繼續部署，每個網域必須從同盟轉換為受控，以便強迫透過密碼雜湊同步處理執行使用者驗證。

> [!IMPORTANT]
> 並非所有網域都需要同時轉換，您可選擇從生產租用戶上的測試網域，或是使用者數量最少的網域開始。

使用 Azure AD PowerShell 模組執行轉換。

   1. 開啟 [PowerShell] 並使用 [全域系統管理員] 帳戶登入 Azure AD。  
   2. 若要轉換第一個網域，請執行下列命令：  
   
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   
   3. 開啟 [Azure AD 入口網站]、選取 [Azure Active Directory]，然後選取 [Azure AD Connect]。
   4. 確認已透過執行下列命令的方式，將網域轉換成受控：
   
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>測試和下一個步驟

### <a name="test-authentication-with-phs"></a>使用 PHS 測試驗證

如果您的租用戶使用同盟，會從 Azure AD 登入頁面將使用者重新導向至您的 AD FS 環境。 現在，租用戶已設定為使用密碼雜湊同步處理，而不是同盟，系統不會將使用者重新導向至 AD FS，而是直接透過 Azure AD 登入頁面登入。

以 InPrivate 模式開啟 Internet Explorer 可避免無縫 SSO 將您自動登入並前往 Office 365 登入頁面 ([http://portal.office.com](http://portal.office.com/))。 輸入使用者的 UPN，然後按一下 [下一步]。 請務必輸入從內部部署 Active Directory 同步，而且先前已建立同盟的混合式使用者 UPN。 使用者會看到須輸入使用者名稱和密碼的畫面。

![圖 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![圖 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

您輸入密碼後，系統應會將您重新導向至 Office 365 入口網站。

![圖 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>測試無縫單一登入

登入連線到公司網路的網域加入機器。 開啟 Internet Explorer，並前往下列其中一個 URL：  
  
‎[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (使用您的網域取代 Contoso)。

系統會立即將使用者重新導向至 Azure AD 登入頁面並顯示「正在嘗試將您登入」訊息，然後應不會提示您輸入使用者名稱或密碼。

![圖 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

然後，會將使用者重新導向並成功登入存取面板：

> [!NOTE]
> 無縫單一登入適用於支援網域提示的 Office 365 服務 (例如 myapps.microsoft.com/contoso.com)。 Office 365 入口網站 (portal.office.com) 目前不支援網域提示，因此會預期使用者必須輸入 UPN。 一旦輸入 UPN，無縫單一登入便可代表使用者擷取 Kerberos 票證並記錄，而且不需要輸入密碼。 

> [!TIP]
> 請考慮[在 Windows 10 上部署混合式 Azure AD Join](https://docs.microsoft.com/azure/active-directory/device-management-introduction)，以改善單一登入體驗。

### <a name="removal-of-the-relying-party-trust"></a>移除信賴憑證者信任

一旦確認所有使用者和用戶端都透過 Azure AD 成功驗證，便可視為安全地移除 Office 365 信賴憑證者信任。

如果 AD FS 未用於其他用途 (已設定其他信賴憑證者信任)，現在即可安全地解除委任 AD FS。

### <a name="rollback"></a>復原

如果找到主要問題而且無法快速解決，您可能需決定將解決方案復原至同盟。

如需特定的部署詳細資料，請參閱您的同盟設計和部署文件。 此流程應該包含：

* 使用 Convert-MSOLDomainToFederated 將受控網域轉換成同盟網域

* 必要時設定其他宣告規則。

### <a name="enable-synchronization-of-userprincipalname-updates"></a>同步處理 userPrincipalName 更新

在過去，除非下列兩項條件都成立，否則皆會將透過內部部署使用同步處理服務的 UserPrincipalName 屬性更新封鎖：

* 使用者受到管理 (非同盟)。

* 使用者尚未指派授權。

如需有關如何確認或啟用這項功能的指示，請參閱下列文章：

[同步處理 userPrincipalName 更新](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)。

### <a name="troubleshooting"></a>疑難排解

您的支援團隊應該了解如何針對從同盟變更至受控期間或之後所引起的任何驗證問題，進行疑難排解。 請使用下列疑難排解說明文件協助您的支援團隊熟悉常見的疑難排解步驟，以及有助於隔離並解決問題的適當動作。

[針對 Azure Active Directory 密碼雜湊同步處理進行疑難排解](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[針對 Azure Active Directory 無縫單一登入進行疑難排解](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>變換無縫 SSO Kerberos 解密

請務必經常變換在內部部署 AD 樹系中建立之 AZUREADSSOACC 電腦帳戶 (這表示 Azure AD) 的 Kerberos 解密金鑰。 強烈建議至少每隔 30 天變換一次 Kerberos 解密金鑰，以便和 Active Directory 網域成員提交密碼變更的方式保持一致。 由於沒有任何相關聯的裝置連接至 AZUREADSSOACC 電腦帳戶物件，因此需要手動執行變換。

請在執行 Azure AD Connect 的內部部署伺服器上執行下列步驟，開始變換 Kerberos 解密金鑰。

[如何才能變換 AZUREADSSOACC 電腦帳戶的 Kerberos 解密金鑰](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)？

## <a name="next-steps"></a>後續步驟

- [Azure AD Connect 的設計概念](plan-connect-design-concepts.md)
- [選擇正確的驗證](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [支援的拓撲](plan-connect-design-concepts.md)
