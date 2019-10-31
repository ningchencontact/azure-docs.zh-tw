---
title: 無密碼安全性金鑰登入內部部署資源（預覽）-Azure Active Directory
description: 啟用無密碼安全性金鑰登入內部部署資源（預覽）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c42bb1e76d854723c8a88e9c3e2c104464beb0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164857"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-preview"></a>啟用無密碼安全性金鑰登入內部部署資源（預覽）

本檔著重于針對同時加入**Azure AD**和**混合式 Azure AD** Windows 10 裝置的環境，啟用內部部署資源的無密碼 authentication。 這項功能會使用與 Microsoft 相容的安全性金鑰，提供對內部部署資源的無縫單一登入（SSO）。

|     |
| --- |
| FIDO2 安全性金鑰是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>使用 FIDO2 金鑰的 SSO 至內部部署資源

Azure Active Directory （AD）可以針對一或多個 Active Directory 網域發出 Kerberos 票證授權票證（Tgt）。 這項功能可讓使用者使用新式認證（例如 FIDO2 安全性金鑰）登入 Windows，並存取傳統的 Active Directory 資源。 Kerberos 服務票證和授權將繼續由內部部署 Active Directory 網域控制站控制。

系統會在您的內部部署 Active Directory 中建立 Azure AD 的 Kerberos 伺服器物件，然後安全地發佈至 Azure Active Directory。 物件未與任何實體伺服器相關聯。 這只是一項資源，可供 Azure Active Directory 用來為您的 Active Directory 網域產生 Kerberos 票證授權票證（Tgt）。

![從 Azure AD 和 AD DS 取得 TGT 和 PRT](./media/howto-authentication-passwordless-on-premises/fido2-tgt-exchange-process.png)

1. 使用者使用 FIDO2 安全性金鑰登入其 Windows 10 裝置，並向 Azure AD 進行驗證。
1. Azure AD 檢查目錄中是否有符合使用者內部部署 AD 網域的 Kerberos 伺服器金鑰。
   1. Azure AD 會為使用者的內部部署 AD 網域產生 Kerberos TGT。 TGT 只會包含使用者的 SID。 TGT 中沒有包含任何授權資料。
1. TGT 會連同其 Azure AD 的主要重新整理權杖（PRT）一起傳回給用戶端。
1. 用戶端機器會聯絡內部部署 AD 網域控制站，並針對完整格式的 TGT 來交易部分 TGT。
1. 用戶端電腦現在具有 Azure AD PRT 和完整的 Active Directory TGT，而且可以存取雲端和內部部署資源。

## <a name="requirements"></a>要求

在完成本文中的步驟之前，組織必須先完成[啟用無密碼 security key sign To Windows 10 裝置（預覽）](howto-authentication-passwordless-security-key.md)一文中的步驟。

組織必須符合下列軟體需求。

- 裝置必須執行 Windows 10 Insider 組建18945或更新版本
- 升級至最新版本的[Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)
- 已完整修補 Windows Server 2016/2019 網域控制站，以處理內部部署驗證要求負載。

### <a name="supported-scenarios"></a>支援的案例

- 此案例支援下列兩者的單一登入（SSO）：
   - 雲端資源，例如 Office 365 和其他啟用 SAML 的應用程式。
   - 內部部署資源和 web sites 的 Windows 整合式驗證，包括需要 IIS 驗證的網站和 SharePoint 網站，以及（或）使用 NTLM 驗證的資源。

### <a name="unsupported-scenarios"></a>不支援的情節

不支援下列案例：

- 已加入 Windows Server Active Directory Domain Services （AD DS）網域（僅限內部部署裝置）部署**不受支援**。
- **不支援**使用安全性金鑰的 RDP、VDI 和 Citrix 案例。
- **不支援**使用安全性金鑰進行 S/MIME。
- 使用安全性金鑰**不支援**「執行身分」。
- **不支援**使用安全性金鑰登入伺服器。

## <a name="create-kerberos-server-object"></a>建立 Kerberos 伺服器物件

系統管理員將使用其 Azure AD Connect 伺服器中的 PowerShell 工具，在其內部部署目錄中建立 Azure AD 的 Kerberos 伺服器物件。 您將需要在組織中包含 Azure AD 使用者的每個網域和樹系中執行這些步驟。

1. 升級至最新版本的 Azure AD Connect。 這些指示假設您已設定 Azure AD Connect 以支援您的混合式環境。
1. 在 Azure AD Connect 伺服器上，開啟已提升許可權的 PowerShell 提示字元，然後流覽至 C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\
1. 執行下列 PowerShell 命令，以在您的內部部署 Active Directory 網域和 Azure Active Directory 租使用者中建立新的 Azure AD Kerberos 伺服器物件。

> [!NOTE]
> 將下列範例中的 "contoso.corp.com" 取代為您的內部部署 Active Directory 功能變數名稱。

```PowerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>查看和驗證 Azure AD Kerberos 伺服器

您可以使用下列命令來查看和驗證新建立的 Azure AD Kerberos 伺服器。 

```PowerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

此命令將輸出 Azure AD Kerberos 伺服器的屬性。 您可以檢查屬性，以確認所有專案都是正確的順序。

| 屬性 | 描述 |
| --- | --- |
| ID | AD 網域控制站物件的唯一識別碼。 此識別碼有時稱為「位置」或其為「分支識別碼」。 |
| DomainDnsName | Active Directory 網域的 DNS 功能變數名稱。 |
| ComputerAccount | Azure AD Kerberos 伺服器物件（DC）的電腦帳戶物件。 |
| UserAccount | 保留 Azure AD Kerberos 伺服器 TGT 加密金鑰的已停用使用者帳戶物件。 此帳戶的 DN 將是： <br> CN = krbtgt_AzureAD，CN = Users，< 網域-DN > |
| KeyVersion | Azure AD Kerberos 伺服器 TGT 加密金鑰的金鑰版本。 建立金鑰時，會指派版本。 每次輪替金鑰時，版本就會遞增。 增量是以複寫中繼資料為基礎，而且可能會大於一。 例如，初始 KeyVersion 可能是192272。 第一次輪替金鑰時，版本可能會前進至212621。 要確認的重要事項是，內部部署物件的 KeyVersion 和雲端物件的 CloudKeyVersion 相同。 |
| KeyUpdatedOn | 更新/建立 Azure AD Kerberos 伺服器 TGT 加密金鑰的日期和時間。 |
| KeyUpdatedFrom | 上次更新 Azure AD Kerberos 伺服器 TGT 加密金鑰的網域控制站。 |
| CloudId | 來自 Azure AD 物件的識別碼。 必須符合上述的識別碼。 |
| CloudDomainDnsName | Azure AD 物件的 DomainDnsName。 必須符合上述的 DomainDnsName。 |
| CloudKeyVersion | Azure AD 物件的 KeyVersion。 必須符合上述的 KeyVersion。 |
| CloudKeyUpdatedOn | Azure AD 物件的 KeyUpdatedOn。 必須符合上述的 KeyUpdatedOn。 |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>輪替 Azure AD Kerberos 伺服器金鑰

Azure AD 的 Kerberos 伺服器加密 krbtgt 金鑰應定期輪替。 建議您依照用來輪替所有其他 Active Directory 網網域控制站 krbtgt 金鑰的相同排程進行。

> [!WARNING]
> 有其他工具可以輪替 krbtgt 金鑰，不過，您必須使用本檔中所述的工具來輪替 Azure AD Kerberos 伺服器的 krbtgt 金鑰。 這可確保金鑰會同時在內部部署 AD 和 Azure AD 中更新。

```PowerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>移除 Azure AD Kerberos 伺服器

如果您想要還原案例，並從內部部署 Active Directory 和 Azure Active Directory 移除 Azure AD Kerberos 伺服器，請執行下列命令。  

```PowerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>多樹系和多網域案例

Azure AD 的 Kerberos 伺服器物件會在 Azure AD 中以 KerberosDomain 物件表示。 每個內部部署 Active Directory 網域都會以 Azure AD 中的單一 KerberosDomain 物件表示。

例如，您的組織有一個 Active Directory 樹系，其中包含 contoso.com 和 fabrikam.com 兩個網域。 如果您選擇允許 Azure AD 針對整個樹系發出 Kerberos 票證授權票證（TGT），Azure AD 中將會有兩個 KerberosDomain 物件。 一個用於 contoso.com 的 KerberosDomain 物件，另一個用於 fabrikam.com。 如果您有多個 Active Directory 樹系，每個樹系中的每個網域都有一個 KerberosDomain 物件。

您必須在組織中包含 Azure AD 使用者的每個網域和樹系中，執行[建立 Kerberos 伺服器物件](#create-kerberos-server-object)中的步驟。

## <a name="known-behavior"></a>已知的行為

如果您的密碼已過期，將會封鎖 FIDO 的登入。 預期會讓使用者重設其密碼，然後才能使用 FIDO 登入。

## <a name="troubleshooting-and-feedback"></a>疑難排解與意見反應

如果您想要在預覽這項功能時分享意見反應或遇到問題，請透過 Windows 意見反應中樞應用程式共用。

1. 啟動**意見反應中樞**，並確認您已登入。
1. 在下列分類下提交意見反應：
   1. 類別：安全性和隱私權
   1. 子類別目錄： FIDO
1. 若要捕獲記錄，請使用 [**重新建立我的問題**] 選項。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-this-work-in-my-on-premises-environment"></a>這會在我的內部部署環境中運作嗎？

這項功能不適用於純粹的內部部署 Active Directory Domain Services （AD DS）環境。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的組織需要雙因素驗證才能存取資源，我該怎麼做才能支援這項需求？

安全性金鑰有各種形式的規格。 請洽詢裝置製造商，以討論如何以 PIN 或生物特徵辨識作為第二個因素來啟用其裝置。

### <a name="can-admins-set-up-security-keys"></a>系統管理員可以設定安全性金鑰嗎？

我們正致力於此功能的公開上市（GA）功能。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>哪裡可以找到相容的安全性金鑰？

[FIDO2 安全性金鑰](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>如果我遺失安全性金鑰，該怎麼辦？

您可以從 Azure 入口網站移除金鑰，方法是流覽至 [安全性資訊] 頁面，並移除安全性金鑰。

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>我在建立混合式 Azure AD 加入的電腦之後，無法立即使用 FIDO

如果安裝混合式 Azure AD 加入的電腦、加入網域後重新開機，則必須使用密碼登入，並等待原則進行同步，才能使用 FIDO 登入。

- 在命令視窗中輸入 `dsregcmd /status`，並檢查 `AzureAdJoined` 和 `DomainJoined` 是否顯示 `YES`，以檢查您目前的狀態。
- 此延遲是已加入網域之裝置的已知限制，而不是 FIDO 特定。

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>我無法在使用 FIDO 登入之後，將 SSO 提供給我的 NTLM 網路資源，並取得認證提示

請確定已修補足夠的網域控制站，以回應您的資源要求。 若要檢查是否可以看到執行此功能的網域控制站，請檢查 nltest/dsgetdc： contoso/keylist/kdc. 的輸出。

## <a name="next-steps"></a>後續步驟

[深入瞭解無密碼](concept-authentication-passwordless.md)
