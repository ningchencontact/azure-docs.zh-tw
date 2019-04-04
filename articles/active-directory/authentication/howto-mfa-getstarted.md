---
title: 計劃和執行 Azure Multi-factor Authentication 部署-Azure Active Directory
description: Microsoft Azure Multi-factor Authentication 部署規劃
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11846ca2a323da5889f444024767df4803a48a51
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892206"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication"></a>規劃雲端架構的 Azure Multi-factor Authentication

在日益複雜的案例中的組織資源所連線的人員。 從開啟和關閉使用智慧型手機、 平板電腦、 Pc 和膝上型電腦，通常在多個平台上的公司網路的組織擁有、 個人及公用裝置，使用者連接。 在此一律連線、 多重裝置和多平台的世界裡，使用者帳戶的安全性是比以往更加重要。 不論其複雜性，跨裝置、 網路及平台使用的密碼，便無法再足以確保使用者帳戶的安全性，尤其是使用者通常會在帳戶間重複使用密碼。 複雜的網路釣魚和其他的社交工程攻擊可能會導致使用者名稱和密碼所張貼和售出 dark web。

[Azure Multi-factor Authentication (MFA)](concept-mfa-howitworks.md)有助於保護資料和應用程式的存取。 它提供一層額外的使用驗證第二種形式的安全性。 組織可以使用[條件式存取](../conditional-access/overview.md)才能使解決方案符合他們的特定需求。

## <a name="prerequisites"></a>必要條件

開始之前部署的 Azure Multi-factor Authentication Server，有必要條件應該考慮的項目。

| 案例 | 必要條件 |
| --- | --- |
| **僅限雲端的**已啟用新式驗證的身分識別環境 | **沒有其他必要工作** |
| **混合式**身分識別案例 | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)部署和使用者身分識別為同步處理，或與內部部署 Active Directory 網域服務與 Azure Active Directory 同盟。 |
| 在內部部署發行以供雲端存取的舊版應用程式 | Azure AD[應用程式 Proxy](../manage-apps/application-proxy.md)部署。 |
| 使用 Azure MFA RADIUS 驗證 | A[網路原則伺服器 (NPS)](howto-mfa-nps-extension.md)部署。 |
| 使用者有適用於 iOS 11 或更早版本的 Microsoft Office 2010 或更早版本或 Apple Mail | 升級至[Microsoft Office 2013 或更新版本](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o)和 Apple mail 適用於 iOS 12 或更新版本。 條件式存取不支援舊版驗證通訊協定。 |

## <a name="plan-user-rollout"></a>規劃使用者首度發行

您的 MFA 首度發行計劃應該包含後面接著您支援的容量內部署波的試驗部署。 開始將您的條件式存取原則套用至一小組試驗使用者的首度發行。 在評估之後對試驗使用者、 處理程序使用，以及註冊行為的影響，您可以新增更多群組原則，或將更多使用者新增至現有的群組。

### <a name="user-communications"></a>使用者通訊

請務必通知使用者，在計劃的通訊中，有關即將發生的變更，Azure MFA 註冊需求，以及任何必要的使用者動作。 我們建議您與您的組織，例如通訊、 變更管理或人力資源部門中的代表一起開發通訊。

Microsoft 提供[溝通範本](https://aka.ms/mfatemplates)並[使用者文件](../user-help/security-info-setup-signin.md)協助編寫您的通訊。 您可以將使用者傳送[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)選取，即可直接註冊**安全性資訊**在該頁面的連結。

## <a name="deployment-considerations"></a>部署考量

您可以強制執行條件式存取原則部署 azure multi-factor Authentication。 A[條件式存取原則](../conditional-access/overview.md)可以要求使用者執行多重要素驗證，例如符合特定條件時：

* 所有使用者、 特定使用者、 群組或已指派角色的成員
* 正在存取的特定雲端應用程式
* 裝置平台
* 裝置狀態
* 網路位置或地理位置的 IP 位址
* 用戶端應用程式
* 登入風險 (需要 Identity Protection)
* 符合規範的裝置
* 已加入混合式 Azure AD 的裝置
* 已核准的用戶端應用程式

條件式存取原則會強制執行註冊，需要未註冊的使用者，以完成註冊，在第一次登入時，一項重要的安全性考量。

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md)參與的註冊原則和 Azure Multi-factor Authentication 劇本的自動化的風險偵測與修復原則。 原則可以建立以強制執行密碼變更，遭入侵的身分識別的威脅時，或登入已被視為高風險由下列時要求 MFA[事件](../reports-monitoring/concept-risk-events.md):

* 認證外洩
* 從匿名 IP 位址登入
* 不可能到達非典型位置的移動
* 從不熟悉的位置登入
* 從受感染的裝置登入
* 從具有可疑活動的 IP 位址登入

某些 Azure Active Directory Identity Protection 偵測到的風險事件會即時進行，一些需要離線處理。 系統管理員可以選擇封鎖使用者，他們會表現有風險的行為和手動修復、 需要變更密碼，或需要進行 multi-factor authentication 做為其條件式存取原則的一部分。

## <a name="define-network-locations"></a>定義網路位置

我們建議組織使用其網路使用定義條件式存取[具名位置](../conditional-access/location-condition.md#named-locations)。 如果您的組織使用 Identity Protection，請考慮使用風險型原則，而不具名的位置。

### <a name="configuring-a-named-location"></a>設定具名的位置

1. 開啟**Azure Active Directory**在 Azure 入口網站
2. 按一下 **條件式存取**
3. 按一下 **具名位置**
4. 按一下 **新位置**
5. 在 **名稱**欄位中，提供有意義的名稱
6. 選取您要定義使用 IP 範圍] 或 [國家/地區的位置
   1. 如果使用 IP 範圍
      1. 決定是否要標示為 「 受信任的位置。 從受信任位置登入可降低使用者的登入風險。 如果您知道輸入的 IP 範圍會建立且可靠，在您的組織中，只能將標示為受信任此位置。
      2. 指定的 IP 範圍
   2. 如果使用的國家/地區
      1. 展開 下拉式選單，選取您想要定義此具名位置的區域的國家/地區。
      2. 決定是否要包含未知的區域。 未知區域是無法對應至國家/地區的 IP 位址。
7. 按一下 [建立] 

## <a name="plan-authentication-methods"></a>規劃驗證方法

系統管理員可以選擇[驗證方法](../authentication/concept-authentication-methods.md)他們想要提供給使用者。 請務必允許超過單一驗證方法，讓使用者擁有可用的備份方法，以防萬一其主要方法無法使用。 下列方法可供系統管理員啟用：

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

在您的行動裝置上的 Microsoft Authenticator 應用程式傳送推播通知。 使用者會看到通知，並選取**核准**來完成驗證。 透過行動裝置應用程式的推播通知會提供使用者影響最小的選項。 因為它們使用的資料連接，而不是電話語音，它們是也最可靠且安全的選項。

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

行動裝置的應用程式，例如 Microsoft Authenticator 應用程式會產生新的 OATH 驗證碼每隔 30 秒。 使用者會在登入介面中輸入此驗證碼。 在電話已資料或行動電話的訊號，可用的行動裝置應用程式的選項。

### <a name="call-to-phone"></a>電話通話

自動的語音撥打給使用者。 使用者接聽電話並按下**#** 核准其驗證電話鍵盤上。 電話通話是絕佳的備份方法，從行動裝置應用程式通知或驗證程式碼。

### <a name="text-message-to-phone"></a>電話簡訊

包含驗證碼的簡訊傳送給使用者，系統會提示使用者在登入介面中輸入驗證碼。

### <a name="choose-verification-options"></a>選擇驗證選項

1. 瀏覽至 **Azure Active Directory**、**使用者**、**Multi-Factor Authentication**。

   ![從 Azure 入口網站中的 [Azure AD 使用者] 刀鋒視窗存取 Multi-Factor Authentication 入口網站](media/howto-mfa-getstarted/users-mfa.png)

1. 在開啟的新索引標籤中，瀏覽至 [服務設定]。
1. 在 [驗證選項] 底下，核取使用者可用方法的所有方塊。

   ![在 [Multi-Factor Authentication 服務設定] 索引標籤中設定驗證方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. 按一下 [ **儲存**]。
1. 關閉 [服務設定] 索引標籤。

## <a name="plan-registration-policy"></a>計劃註冊原則

系統管理員必須決定使用者將如何註冊其方法。 組織應[啟用新的合併的註冊體驗](howto-registration-mfa-sspr-combined.md)Azure MFA 和自助式密碼重設 (SSPR)。 SSPR 可讓使用者重設其密碼安全的方式，使用相同的方法，他們使用 multi-factor authentication。 我們建議這樣結合在一起註冊，目前在公開預覽，因為它是使用者，能夠一次註冊這兩項服務的絕佳體驗。 SSPR 和 Azure MFA 啟用相同的方法，可讓使用者註冊才能使用這兩項功能。

### <a name="registration-with-identity-protection"></a>註冊使用 Identity Protection

如果您的組織使用 Azure Active Directory Identity Protection，[設定 MFA 註冊原則](../identity-protection/howto-mfa-policy.md)提示使用者註冊下一次以互動方式登入。

### <a name="registration-without-identity-protection"></a>註冊沒有身分識別保護

如果您的組織沒有啟用 Identity Protection 的授權，系統會提示使用者註冊會在登入時需要 MFA 的下一次。 如果將不會使用以 MFA 保護的應用程式使用者可能未註冊 mfa。 請務必取得的所有使用者註冊以便不良執行者無法猜出使用者的密碼，並代表他們註冊 MFA 有效地掌控帳戶。

#### <a name="enforcing-registration"></a>強制執行註冊

使用下列步驟的條件式存取原則可以強制使用者註冊 Multi-factor Authentication

1. 建立群組，新增所有目前未註冊的使用者。
2. 使用條件式存取，強制執行多重要素驗證，此群組的所有資源的存取權。
3. 請定期重新評估群組的成員資格，並從群組中移除已註冊的使用者。

您可能會識別已註冊和未註冊 Azure MFA 使用者時依賴的 PowerShell 命令[MSOnline PowerShell 模組](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)。

#### <a name="identify-registered-users"></a>識別已註冊的使用者

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>識別未註冊的使用者

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

## <a name="plan-conditional-access-policies"></a>計劃條件式存取原則

若要規劃您的條件式存取原則策略，將會決定 MFA 和其他控制項需要時，請參閱[什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)。

很重要，您就會避免不慎鎖定 Azure AD 租用戶。 您可以減少這個不慎失去系統管理存取權的影響[租用戶中建立兩個或多個緊急存取帳戶](../users-groups-roles/directory-emergency-access.md)並從您的條件式存取原則中排除它們。

### <a name="create-conditional-access-policy"></a>建立條件式存取原則

1. 使用全域系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 瀏覽至**Azure Active Directory**，**條件式存取**。
1. 選取 [新增原則]。
1. 為原則提供有意義的名稱。
1. 在 [使用者和群組] 底下：
   * 在 [包含] 索引標籤上，選取 [所有使用者] 選項按鈕
   * 在 **排除**索引標籤上，核取方塊**使用者和群組**，然後選擇 緊急存取帳戶。
   * 按一下 [完成] 。
1. 在 [雲端應用程式] 下方，選取 [所有雲端應用程式] 選項按鈕。
   * 選擇性：在 [排除] 索引標籤上，選擇組織不要要求使用 MFA 的雲端應用程式。
   * 按一下 [完成] 。
1. 在 [條件] 區段底下：
   * 選擇性：如果您已啟用 Azure Identity Protection，您可以選擇要在原則中評估登入風險。
   * 選擇性：如果您已設定信任的位置或具名位置，您可以指定要在原則中包含或排除這些位置。
1. 在 [授與] 下方，確定已選取 [授與存取權] 選項按鈕。
    * 核取 [需要多重要素驗證] 的方塊。
    * 按一下 [選取] 。
1. 略過 [工作階段] 區段。
1. 將 [啟用原則] 切換為 [開啟]。
1. 按一下頁面底部的 [新增] 。

![建立條件式存取原則，以對試驗群組中的 Azure 入口網站使用者啟用 MFA](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>計劃與內部部署系統的整合

不直接對 Azure AD 驗證某些舊系統還是內部部署應用程式需要額外的步驟，可使用 MFA 包括：

* 舊版內部部署應用程式，必須使用應用程式 proxy。
* 在內部部署 RADIUS 的應用程式，將需要使用 MFA 配接器與 NPS 伺服器。
* 內部部署 AD FS 的應用程式，將需要 AD FS 2016 中使用 MFA 配接器。

請直接使用 Azure AD 進行驗證和新式驗證 （Ws-fed、 SAML、 OAuth、 OpenID Connect） 的應用程式可以直接使用條件式存取原則。

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 中使用 Azure MFA

應用程式內部可以發行至您的 Azure AD 租用戶透過[Azure AD 應用程式 Proxy](../manage-apps/application-proxy.md)並可以利用 Azure Multi-factor Authentication 設定為使用 Azure AD 預先驗證。

這些應用程式受限於強制 Azure Multi-factor Authentication，就像任何其他 Azure AD 整合應用程式的條件式存取原則。

同樣地，如果 Azure Multi-factor Authentication 會對所有的使用者登入強制執行，在內部使用 Azure AD 應用程式 Proxy 發佈應用程式將會受到保護。

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Azure Multi-factor Authentication 整合網路原則伺服器

Azure MFA 的網路原則伺服器 (NPS) 擴充功能可使用現有伺服器將雲端式 MFA 功能新增至驗證基礎結構。 NPS 擴充功能，您可以在現有的驗證流程新增通話、 簡訊或電話應用程式驗證。 這項整合有下列限制：

* CHAPv2 通訊協定，只有 「 驗證器應用程式推播通知 」 和 「 語音電話支援。
* 無法套用條件式存取原則。

NPS 擴充功能作為 RADIUS 與雲端 Azure MFA 提供第二因素驗證來保護之間的介面卡[VPN](howto-mfa-nps-extension-vpn.md)，[遠端桌面閘道連線](howto-mfa-nps-extension-rdg.md)，或其他 RADIUS 支援應用程式。 在此環境中的註冊 Azure MFA 會受到質疑的所有驗證嘗試的使用者，MFA 的條件式存取原則平均值缺乏總是必要。

#### <a name="implementing-your-nps-server"></a>實作您的 NPS 伺服器

如果您部署 NPS 執行個體，並在使用中，參考[整合您現有的 NPS 基礎結構與 Azure Multi-factor Authentication](howto-mfa-nps-extension.md)。 如果您要設定 NPS，第一次，請參閱[網路原則伺服器 (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)如需相關指示。 可以找到一文中的疑難排解指引[解決 Azure Multi-factor Authentication 的 NPS 擴充功能的錯誤訊息](howto-mfa-nps-extension-errors.md)。

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>準備 NPS 未註冊 MFA 的使用者

選擇未註冊 mfa 的使用者嘗試驗證時，會發生什麼事。 使用登錄設定`REQUIRE_USER_MATCH`登錄路徑中`HKLM\Software\Microsoft\AzureMFA`來控制功能的行為。 此設定具有單一組態選項。

| Key | 值 | 預設值 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | 未設定 (相當於 TRUE) |

此設定的目的是要決定當使用者未註冊 MFA 時的行為。 下表詳列變更此設定的效果。

| 設定 | 使用者的 MFA 狀態 | 效果 |
| --- | --- | --- |
| 索引鍵不存在 | 未註冊 | MFA 挑戰不成功 |
| 值設定為 True / 未設定 | 未註冊 | MFA 挑戰不成功 |
| 索引鍵設定為 False | 未註冊 | 不使用 MFA 驗證 |
| 索引鍵設定為 False 或 True | 註冊 | 必須使用 MFA 進行驗證 |

### <a name="integrate-with-active-directory-federation-services"></a>整合 Active Directory Federation Services

如果您的組織與 Azure AD 同盟，您可以使用[來保護 AD FS 資源的 Azure Multi-factor Authentication](multi-factor-authentication-get-started-adfs.md)，同時對內部部署和雲端中。 Azure MFA 可讓您減少密碼，並提供更安全的方式來進行驗證。 從 Windows Server 2016 開始，您現在可以設定 Azure MFA 進行主要驗證。

不同於與 Windows Server 2012 r2 中的 AD FS 的 AD FS 2016 Azure MFA 配接器直接與 Azure AD 整合且不需要內部部署 Azure MFA server。 Azure MFA 配接器內建於 Windows Server 2016 中，並不需要額外的安裝。

當使用 Azure MFA 與 AD FS 2016 和目標應用程式都必須遵守條件式存取原則時，還有其他考量：

* 應用程式是信賴憑證者合作對象到 Azure AD，與 AD FS 2016 同盟時，使用條件式存取。
* 條件式存取時不使用應用程式是信賴憑證者的合作對象，將 AD FS 2016 和受控或同盟與 AD FS 2016。
* 條件式存取時也不使用 AD FS 2016 設定為使用 Azure MFA 的主要驗證方法。

#### <a name="ad-fs-logging"></a>AD FS 記錄

標準 AD FS 2016 登入 Windows 安全性記錄檔和 AD FS 系統管理員記錄檔中，包含驗證要求及成功或失敗的相關資訊。 這些事件內的事件記錄檔資料會指出是否已使用 Azure MFA。 例如，AD FS 稽核的事件識別碼 1200年可能包含：

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>更新和管理憑證

在每個 AD FS 伺服器上，在我的儲存體，在本機電腦將自我簽署的 Azure MFA 憑證標題為的 OU = Microsoft AD FS Azure MFA，其中包含憑證到期日。 請檢查此憑證來決定到期日的每個 AD FS 伺服器上的有效期間。

如果您的憑證有效期間即將到期日[產生和驗證每個 AD FS 伺服器上新的 MFA 憑證](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)。

下列指引詳細說明如何管理您的 AD FS 伺服器上的 Azure MFA 憑證。 當您設定 AD FS 與 Azure MFA 時，憑證會產生透過`New-AdfsAzureMfaTenantCertificate`PowerShell cmdlet 的有效期為 2 年。 更新，並在訂閱到期前的更新的憑證安裝到 ovoid MFA 服務中斷。

## <a name="implement-your-plan"></a>實作您的計劃

既然您已規劃您的解決方案，您可以實作下列步驟：

1. 符合任何所需的必要條件
   1. 部署[Azure AD Connect](../hybrid/whatis-hybrid-identity.md)任何混合式案例
   1. 部署[Azure AD 應用程式 Proxy](../manage-apps/application-proxy.md)的任何內部部署應用程式發行以供雲端存取
   1. 部署[NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)任何 RADIUS 驗證
   1. 請確定使用者就會有升級支援的 Microsoft Office 版本中，已啟用新式驗證
1. 設定所選[驗證方法](#choose-verification-options)
1. 定義您[具名網路位置](../conditional-access/location-condition.md#named-locations)
1. 選取群組以開始推出 MFA。
1. 設定您[條件式存取原則](#create-conditional-access-policy)
1. 設定 MFA 註冊原則
   1. [合併的 MFA 和 SSPR](howto-registration-mfa-sspr-combined.md)
   1. 使用[Identity Protection](../identity-protection/howto-mfa-policy.md)
1. 傳送使用者通訊以及取得使用者在註冊 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [追蹤誰已註冊的](#identify-non-registered-users)

## <a name="manage-your-solution"></a>管理您的解決方案

Azure MFA 的報表

Azure Multi-factor Authentication 提供透過 Azure 入口網站的報表：

| 報告 | 位置 | 描述 |
| --- | --- | --- |
| 使用方式和詐騙警示 | Azure AD > 登入 | 提供整體使用量、使用者摘要和使用者詳細資料的相關資訊；以及在指定的日期範圍期間所提交的詐騙警示歷程記錄。 |

## <a name="troubleshoot-mfa-issues"></a>針對 MFA 問題進行疑難排解

尋找解決方案，常見的問題，與在 Azure MFA[疑難排解 Azure Multi-factor Authentication 文件](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)在 Microsoft 支援中心。

## <a name="next-steps"></a>後續步驟

* [驗證方法有哪些？](concept-authentication-methods.md)
* [啟用 Azure Multi-factor Authentication 與 Azure AD 自助式密碼重設的聚合式的註冊](concept-registration-mfa-sspr-converged.md)
* 系統會還是不會提示使用者執行 MFA 的原因？ 請參閱＜Azure Multi-Factor Authentication 中的報告＞文件中的 [Azure AD 登入報告](howto-mfa-reporting.md#azure-ad-sign-ins-report)一節。