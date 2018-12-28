---
title: Azure AD Connect：從同盟移轉至 Azure AD 的傳遞驗證 | Microsoft Docs
description: 將混合式身分識別環境從同盟移至傳遞驗證的相關資訊。
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
ms.openlocfilehash: 5361c8940c8c7dba5338a3f5a0ed18910f7e45a0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410324"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>從同盟移轉至 Azure AD 的傳遞驗證遷移
下列文件提供從 AD FS 移至傳遞驗證的相關指引。

>[!NOTE]
>您可以在[此處](https://aka.ms/ADFSTOPTADPDownload)下載本文件複本。

## <a name="prerequisites-for-pass-through-authentication"></a>傳遞驗證的先決條件
移轉之前必須符合下列先決條件。
### <a name="update-azure-ad-connect"></a>更新 Azure AD Connect

若要成功執行移轉到傳遞驗證的步驟，至少應有 [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0。 此版本包含執行登入轉換方式的重大變更，而且可將從同盟移轉到雲端驗證的整體時間從數小時縮減至數分鐘。

> [!IMPORTANT]
> 過時的文件、工具和部落格文章上，註明了當從同盟網域轉換成受控網域時，使用者轉換是必要的步驟。 不再需要**轉換使用者**，而且 Microsoft 正致力更新說明文件和工具來反映此項變更。

若要將 Azure AD Connect 更新至最新版本，請依照本[更新指示](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)操作。

### <a name="plan-authentication-agent-number-and-placement"></a>規劃驗證代理程式編號和位置

在 Azure AD Connect 伺服器以及內部部署 Windows 伺服器上部署輕量型代理程式，即可完成傳遞驗證。 代理程式的安裝位置盡可能靠近 Active Directory 網域控制站，可以降低延遲。

對大部分的客戶來說，二到三個驗證代理程式就足以提供高可用性和容量，而租用戶可以註冊 12 個以內的代理程式。 第一個代理程式一律安裝在 AAD Connect 伺服器上。 請參閱[網路流量估計和效能指引資訊](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations)，了解代理程式的限制和代理程式的部署選項。

### <a name="plan-migration-method"></a>規劃移轉方法

有兩種方法可以從同盟驗證移轉至 PTA 和無縫 SSO。 您使用的方法將取決於原本設定 AD FS 的方式。

- **使用 Azure AD Connect**。 如果原本使用 Azure AD Connect 設定 AD FS，則*必須*透過 Azure AD Connect 精靈來執行傳遞驗證的變更。

使用 Azure AD Connect 時，若要變更使用者登入方式，會自動為您執行 Set-MsolDomainAuthentication Cmdlet，因此在 Azure AD 租用戶中所有已驗證同盟網域解除同盟的情況下，您就沒有控制權。  
‎  
> [!NOTE]
> 此時，如果原先使用 AAD Connect 為您設定 AD FS，將使用者登入變更成傳遞驗證時會無法避免解除租用戶中所有網域的同盟。  
‎
- **使用 Azure AD Connect 和 PowerShell**。 只有在原先並非以 Azure AD Connect 設定 AD FS 的情況下，才可使用此方法。 您仍需要透過 Azure AD Connect 精靈變更使用者登入方法，但主要差異在於此方式不會自動執行 Set-MsolDomainAuthentication Cmdlet，因為此方式不知道您的 AD FS 伺服器陣列，所以您可以完全控制要轉換的網域和順序。

若要了解您應該使用何種方式，請執行下一節中的步驟。

#### <a name="verify-current-user-sign-in-settings"></a>請確認目前的使用者登入設定

使用全域系統管理員帳戶登入 Azure AD 入口網站[https://aad.portal.azure.com](https://aad.portal.azure.com/)，確認您目前的使用者登入設定。 

在 [使用者登入] 區段中，確認 [同盟] 設為 [已啟用]，而 [無縫單一登入] 和 [傳遞驗證] 設為 [已停用]。 

![圖 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>確認同盟的設定方式

   1. 前往您的 Azure AD Connect 伺服器並啟動 Azure AD Connect，然後選取 [設定]。
   2. 在 [其他工作] 畫面中，選取 [檢視目前的設定]，然後選取 [下一步]。</br>
   ![圖 31](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. 在 [Review Your Solution]\(檢視您的解決方案\) 畫面中，向下捲動至 [Active Directory Federation Services (AD FS)]。</br>
   如果您在此區段看到 AD FS 設定，可以安心假設原先是透過 Azure AD Connect 設定 AD FS，因此可以透過 Azure AD Connect 的 [變更使用者登入] 選項將同盟網域轉換至受控網域；本節的**選項 1：使用 Azure AD Connect 設定傳遞驗證**會詳述此程序。  
‎
   4. 如果看不到目前設定中所列的 Active Directory 同盟服務，則必須透過 PowerShell 將同盟網域手動轉換至受控網域，此程序會在**選項 2 - 使用 Azure AD Connect 和 PowerShell 從同盟轉換至 PTA**一節中詳述。

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


驗證可能已針對同盟設計和部署說明文件所自訂的任何設定，尤其是 **PreferredAuthenticationProtocol**、**SupportsMfa** 及 **PromptLoginBehavior**。

以下提供這些設定相關功能的詳細資訊。

[Active Directory 同盟服務提示=登入參數支援](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> 如果 SupportsMfa 值目前設為「True」，表示您使用的是內部部署的 MFA 解決方案，會將第二個因素挑戰納入使用者驗證流程中。 如此就不再適用於 Azure AD 的驗證情節，而您必須改用 Azure MFA (雲端式) 服務來執行相同的函式。 繼續進行之前，請先仔細評估您的 MFA 需求，並確定您了解如何運用 Azure MFA、授權意涵以及轉換網域之前的一般使用者註冊程序。

#### <a name="backup-federation-settings"></a>備份同盟設定

雖然在此程序期間不會變更 AD FS 伺服器陣列上的其他信賴憑證者，但建議先確定您的 AD FS 伺服器陣列目前擁有有效備份可供還原。 您可以使用免費的 Microsoft [AD FS 快速還原工具](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)來還原。 可使用這項工具將 AD FS 備份和還原到現有的伺服陣列或新的伺服器陣列。

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
| AD FS 是高度自訂且仰賴 onload.js 檔案中的特定自訂設定 (此檔案無法在 Azure AD 中複製) (例如您已變更登入體驗，所以使用者只能輸入 SamAccountName 格式的使用者名稱，而不是 UPN，或是有大量品牌化的登入體驗)| 您必須確認 Azure AD 可以滿足您目前的自訂需求後再繼續。 如需詳細資訊和指引，請參閱 AD FS 商標和 AD FS 自訂區段。|
| 您正透過 AD FS 封鎖舊版驗證用戶端。| 請考慮以[舊版驗證的條件式存取控制](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)和 [Exchange Online 用戶端存取規則](http://aka.ms/EXOCAR)來更換控制項，以封鎖目前在 AD FS 上的舊版驗證用戶端。 |
| 您要求使用者向 AD FS 進行驗證時，對內部部署 MFA 伺服器解決方案執行 MFA。| 您無法透過內部部署 MFA 解決方案將 MFA 挑戰納入受控網域的驗證流程，但是可以在網域轉換後使用 Azure MFA 服務繼續執行此操作。 如果使用者目前未使用 Azure MFA，會涉及到一次性的使用者註冊步驟，您必須做好準備並傳達給使用者。 |
| 您目前使用 AD FS 中的存取控制原則 (AuthZ 規則) 來控管 Office 365 的存取權。| 請考慮將這些更換成對等的 Azure AD [條件式存取原則](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)和 [Exchange Online 用戶端存取規則](http://aka.ms/EXOCAR)。|

### <a name="considerations-for-common-ad-fs-customizations"></a>常見的 AD FS 自訂考量

#### <a name="inside-corporate-network-claim"></a>公司網路內部宣告

如果使用者在公司網路內部進行驗證，就會由 AD FS 發出 InsideCorporateNetwork 宣告。 此宣告可以傳遞至 Azure AD，並根據使用者的網路位置用來略過多重要素驗證。 如需進一步了解如何判斷目前是否在 AD FS 中啟用此功能，請參閱[同盟使用者適用的可信任 IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)。

一旦您的網域都轉換成傳遞驗證，便再也無法使用 InsideCorporateNetwork 宣告。 可使用 [Azure AD 中的具名位置](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)來取代這項功能。

一旦設定了具名位置，所有已設定要包含/排除網路位置「所有可信任位置」和「MFA 可信任 IP」的條件式存取原則都必須更新，以反映新建立的具名位置。

如需條件式存取中位置條件的詳細資訊，請參閱 [Active Directory 條件式存取位置](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。

#### <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

將裝置加入 Azure AD 可讓您建立條件式存取規則，強制裝置符合您對於安全性與合規性的存取標準，而且可讓使用者登入使用組織的公司或學校帳戶之裝置，而不是個人帳號。 混合式 Azure AD 已加入裝置可讓您將已加入 AD 網域的裝置加入 Azure AD。 可能已使用此功能設定同盟環境。

為了確保網域轉換至傳遞驗證後，混合式加入仍然適用於加入此網域的任何新裝置，必須將 Azure AD Connect 設定為同步 Windows 10 適用的 Active Directory 電腦帳戶和 Azure AD。 若是 Windows 7 和 Windows 8 的電腦帳戶，混合式加入會使用無縫 SSO 註冊 Azure AD 中的電腦，您就不必像 Windows 10 裝置那樣同步處理這些帳戶。 但是您必須將更新的 workplacejoin.exe 檔案 (透過 .msi) 部署到這些舊版用戶端，讓他們可以使用無縫 SSO 自行註冊。 [下載 .msi](https://www.microsoft.com/download/details.aspx?id=53554)。

如需此需求的詳細資訊，請參閱[如何設定已加入 Azure Active Directory 的混合式裝置](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)

#### <a name="branding"></a>商標

您的組織可能會有[自訂的 ADFS 登入頁面](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)來顯示更多有關組織的資訊。 若是如此，可考慮製作相似的[自訂 Azure AD 登入頁面](https://docs.microsoft.com/azure/active-directory/customize-branding)。

使用類似的自訂時，外觀上應該會有些許改變。 建議在與一般使用者的通訊中加入預期內的改變。

> [!NOTE]
> 公司商標只有在您已購買 Azure AD 的「進階」或「基本」授權，或擁有 Office 365 授權時，才可供使用。

## <a name="planning-for-smart-lockout"></a>規劃智慧鎖定

Azure AD 智慧鎖定在使用傳遞驗證，而且在 Active Directory 中設定帳戶鎖定群組原則時，防範暴力密碼破解攻擊，並防止內部部署 Active Directory 帳戶遭到鎖定。 

深入了解[智慧鎖定功能以及如何編輯其設定](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)的詳細資訊。

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

部署傳遞驗證和無縫 SSO 之後，存取 Office 365 以及透過 Azure AD 驗證的其他相關聯資源時，會變更使用者登入體驗。 外部網路的使用者現在只會看到 Azure AD 登入頁面，而不是重新導向至外部 Web 應用程式 Proxy 伺服器所呈現的表單式頁面。

通知的策略規劃包含多項要素。 其中包含：

* 透過以下方式通知使用者即將推出和已發行的功能
  * 電子郵件和其他內部通訊管道
  * 海報等視覺物件
  * 執行即時或其他通訊
* 決定自訂人員、傳送通訊人員以及時間。

## <a name="implementing-your-solution"></a>實作您的解決方案

既已規劃了解決方案，便準備進一步實作。 實作包含下列要素：

   1. 準備無縫單一登入
   2. 將登入方法變更成傳遞驗證，並啟用無縫 SSO

## <a name="step-1--prepare-for-seamless-sso"></a>步驟 1 - 準備無縫 SSO

若是您的裝置要使用無縫 SSO，則必須使用 Active Directory 中的群組原則，將 Azure AD URL 新增至使用者的內部網路區域設定。

瀏覽器預設會自動從指定的 URL 計算正確的區域 (網際網路或內部網路)。 例如，"http://contoso/" 會對應到內部網路區域，而 "http://intranet.contoso.com/" 則會對應到網際網路區域 (因為 URL 包含句點)。 除非將 URL 明確地新增至瀏覽器的內部網路區域，否則瀏覽器不會將 Kerberos 票證傳送給雲端端點 (例如 Azure AD URL)。

請依照[步驟將必要變更](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)套用到您的裝置。

> [!IMPORTANT]
> 執行這項變更不會修改使用者登入 Azure AD 的方式。 但是，繼續執行步驟 3 之前請將此設定套用到所有裝置，這點非常重要。另外請注意，使用者登入未收到這項設定的裝置時，只需輸入使用者名稱和密碼即可登入 Azure AD。

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>步驟 2 – 將登入方法變更為 PTA 並啟用無縫 SSO

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>選項 A：使用 Azure AD Connect 設定 PTA

如果最初使用 Azure AD Connect 設定 AD FS，請使用此方法。 如果最初並非使用 Azure AD Connect 設定 AD FS，就無法使用此方法。

> [!IMPORTANT]
> 請記住，按照下列步驟操作後，所有網域都會從同盟轉換成受控。 如需詳細資訊，請查看「規劃移轉方法」一節。[](#_Plan_Migration_Method)

首先，您必須變更登入方法：

   1. 在 Azure AD Connect 伺服器上開啟精靈。
   2. 選取 [變更使用者登入]，然後選取 [下一步]。 
   3. 在 [Connect to Azure AD] \(連線到 Azure AD\) 畫面中，提供全域系統管理員的使用者名稱和密碼。
   4. 在 [使用者登入] 畫面中，將選項按鈕從 [與 AD FS 同盟] 變更成 [傳遞驗證]、選取 [啟用單一登入]，然後選取 [下一步]。
   5. 在 [啟用單一登入] 畫面中，輸入網域系統管理員帳戶的認證，然後選取 [下一步]。  

   > [!NOTE]
   > 需要網域系統管理員認證才能啟用無縫單一登入，因為此程序會執行下列這些需要較高權限的動作。 網域管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中。 這些只能用來啟用此功能，而且成功完成後即捨棄
   >
   > * 名為 AZUREADSSOACC 的電腦帳戶 (代表 Azure AD) 是在您的內部部署 Active Directory (AD) 中建立。
   > * 電腦帳戶的 Kerberos 解密金鑰可安全地與 Azure AD 共用。
   > * 此外，系統會建立兩個 Kerberos 服務主體名稱 (SPN)，以代表 Azure AD 登入期間所使用的兩個 URL。
   > * 網域管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中。 這些只能用來啟用此功能，而且成功完成後即捨棄

   6. 在 [準備設定] 畫面中，請確定已選取 [設定完成時啟動同步處理程序] 核取方塊。 然後選取 [設定]。</br>
   ![圖片](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. 開啟 [Azure AD 入口網站]、選取 [Azure Active Directory]，然後選取 [Azure AD Connect]。
   8. 確認同盟 [已停用]，同時 [無縫單一登入] 和 [傳遞驗證] 設為 [已啟用]。</br>
   ![圖片](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

接下來，您必須部署額外的驗證方法。 開啟 [Azure 入口網站]，瀏覽至 [Azure Active Directory]、[Azure AD Connect]，然後按一下 [傳遞驗證]。

從 [傳遞驗證]頁面上，按一下 [下載] 按鈕。 從 [下載] 代理程式畫面中，按一下 [接受條款並下載]。

會開始下載額外的驗證代理程式。 在已加入網域的伺服器上安裝第二個驗證代理程式。 

> [!NOTE]
> 第一個代理程式一律會安裝在 Azure AD Connect 伺服器，作為在 Azure AD Connect 工具的「使用者登入」一節中所做的部分設定變更。 任何額外的驗證代理程式應安裝在不同的伺服器上。 建議使用 2 至 3 個額外的驗證代理程式。 

執行驗證代理程式安裝。 在安裝期間，您必須提供**全域系統管理員**帳戶的認證。

![圖 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![圖 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

驗證代理程式安裝之後，您可以返回傳遞驗證代理程式健康情況頁面檢查其他代理程式的狀態。


請前往測試和下一個步驟。

> [!IMPORTANT]
> 略過「選項 B」一節：使用 Azure AD Connect 和 PowerShell 從同盟轉換至 PTA，因為該節中的步驟不適用。  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>選項 B - 使用 Azure AD Connect 和 PowerShell 從同盟轉換至 PTA

如果您的同盟一開始不是使用 Azure AD Connect 設定，請使用此選項。 首先，您必須啟用傳遞驗證：

   1. 在 Azure AD Connect 伺服器上開啟精靈。
   2. 選取 [變更使用者登入]，然後選取 [下一步]。
   3. 在 [Connect to Azure AD] \(連線到 Azure AD\) 畫面中，提供全域系統管理員的使用者名稱和密碼。
   4. 在 [使用者登入] 畫面中，將選項按鈕從 [不設定] 變更成 [傳遞驗證]、選取 [啟用單一登入]，然後選取 [下一步]。
   5. 在 [啟用單一登入] 畫面中，輸入網域系統管理員帳戶的認證，然後選取 [下一步]。

   > [!NOTE]
   > 需要網域系統管理員認證才能啟用無縫單一登入，因為此程序會執行下列這些需要較高權限的動作。 網域管理員認證不會儲存在 Azure AD Connect 或 Azure AD 中。 這些只能用來啟用此功能，而且成功完成後即捨棄。
   >
   > * 名為 AZUREADSSOACC 的電腦帳戶 (代表 Azure AD) 是在您的內部部署 Active Directory (AD) 中建立。
   > * 電腦帳戶的 Kerberos 解密金鑰可安全地與 Azure AD 共用。
   > * 此外，系統會建立兩個 Kerberos 服務主體名稱 (SPN)，以代表 Azure AD 登入期間所使用的兩個 URL。

   6. 在 [準備設定] 畫面中，請確定已選取 [設定完成時啟動同步處理程序] 核取方塊。 然後選取 [設定]。</br>
   ‎![圖片](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   選取 [設定] 時，就會執行下列步驟：
   * 安裝第一個傳遞驗證代理程式
   * 啟用傳遞功能
   * 啟用無縫單一登入。  
   7. 確認 [同盟] 保持 [已停用]，而 [無縫登入] 和 [傳遞驗證] 現在都是 [已啟用]。
   ![圖 2](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. 選取 [傳遞驗證] 並確認狀態為 [使用中]。</br>
   
   如果驗證代理程式未啟用，請遵循[這些疑難排解步驟](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)，再繼續進行下一個步驟中的網域交談流程。 如果在驗證 PTA 代理程式已成功安裝之前轉換網域，而且其狀態在 Azure 入口網站中顯示為「使用中」，則可能導致驗證中斷。  
   9. 接下來部署額外的驗證代理程式。 開啟 [Azure 入口網站]，瀏覽至 [Azure Active Directory]、[Azure AD Connect]，然後按一下 [傳遞驗證]。
   10. 從 [傳遞驗證]頁面上，按一下 [下載] 按鈕。 從 [下載代理程式] 畫面中，按一下 [接受條款並下載]。
   
   會開始下載額外的驗證代理程式。 在已加入網域的伺服器上安裝第二個驗證代理程式。

  > [!NOTE]
  > 第一個代理程式一律會安裝在 Azure AD Connect 伺服器，作為在 Azure AD Connect 工具的「使用者登入」一節中所做的部分設定變更。 任何額外的驗證代理程式應安裝在不同的伺服器上。 建議使用 2 至 3 個額外的驗證代理程式。
  
   11. 執行驗證代理程式安裝。 在安裝期間，您必須提供**全域系統管理員**帳戶的認證。</br>
   ![圖 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![圖 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. 驗證代理程式安裝之後，您可以返回傳遞驗證代理程式健康情況頁面檢查其他代理程式的狀態。

此時，同盟仍是已啟用狀態，而且可供您的網域運作。 若要繼續部署，每個網域必須從同盟轉換為受控，如此傳遞驗證便可開始為網域提供驗證要求。

並非所有網域都需要同時轉換，您可選擇從生產租用戶上的測試網域，或是使用者數量最少的網域開始。

使用 Azure AD PowerShell 模組執行轉換。

   1. 開啟 [PowerShell] 並使用 [全域系統管理員] 帳戶登入 Azure AD。
   2. 若要轉換第一個網域，請執行下列命令：
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. 開啟 [Azure AD 入口網站]、選取 [Azure Active Directory]，然後選取 [Azure AD Connect]。  
   4. 一旦轉換了所有同盟網域，請確認同盟 [已停用]，同時 [無縫單一登入] 和 [傳遞驗證] 設為 [已啟用]。</br>
   ![圖片](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>測試和下一個步驟

### <a name="test-pass-through-authentication"></a>測試傳遞驗證 

如果您的租用戶使用同盟，會從 Azure AD 登入頁面將使用者重新導向至您的 AD FS 環境。 現在，租用戶已設定為使用傳遞驗證，而不是同盟，系統不會將使用者重新導向至 AD FS，而是直接透過 Azure AD 登入頁面登入。

以 InPrivate 模式開啟 Internet Explorer 可避免無縫 SSO 將您自動登入並前往 Office 365 登入頁面 ([http://portal.office.com](http://portal.office.com/))。 輸入使用者的 **UPN**，然後按一下 [下一步]。 請務必輸入從內部部署 Active Directory 同步，而且先前已建立同盟的混合式使用者 UPN。 使用者會看到須輸入使用者名稱和密碼的畫面。

![圖 18](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![圖 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

您輸入密碼後，系統應會將您重新導向至 Office 365 入口網站。

![圖 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>測試無縫單一登入

   1. 登入連線到公司網路的網域加入機器。 
   2. 開啟 **Internet Explorer** 或 **Chrome**，並前往下列其中一個 URL：   
      ‎  
      ‎[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (使用您的網域取代 Contoso)。

      系統會立即將使用者重新導向至 Azure AD 登入頁面並顯示「正在嘗試將您登入」訊息，然後應該不會提示您輸入使用者名稱或密碼。</br>
   ![圖 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. 然後，會將使用者重新導向並成功登入存取面板：

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

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>變換無縫 SSO Kerberos 解密金鑰

請務必經常變換在內部部署 AD 樹系中建立之 AZUREADSSOACC 電腦帳戶 (這表示 Azure AD) 的 Kerberos 解密金鑰。 強烈建議至少每隔 30 天變換一次 Kerberos 解密金鑰，以便和 Active Directory 網域成員提交密碼變更的方式保持一致。 由於沒有任何相關聯的裝置連接至 AZUREADSSOACC 電腦帳戶物件，因此需要手動執行變換。

請在執行 Azure AD Connect 的內部部署伺服器上執行下列步驟，開始變換 Kerberos 解密金鑰。

[如何才能變換 AZUREADSSOACC 電腦帳戶的 Kerberos 解密金鑰](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)？

## <a name="monitoring-and-logging"></a>監視和記錄

應監控執行驗證代理程式的伺服器，以維護解決方案的可用性。 除了一般的伺服器效能計數器，驗證代理程式還會公開可用來了解驗證統計資料和錯誤的效能物件。

驗證代理程式會將作業記錄到「Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin」底下的 Windows 事件記錄檔。

必要時可以啟用疑難排解記錄檔。

請參閱關於[監視和記錄](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication)的詳細資訊。

## <a name="next-steps"></a>後續步驟

- [Azure AD Connect 的設計概念](plan-connect-design-concepts.md)
- [選擇正確的驗證](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [支援的拓撲](plan-connect-design-concepts.md)
