---
title: 使用 Azure Active Directory 中設定應用程式的群組宣告 |Microsoft Docs
description: 有關如何與 Azure AD 中設定使用的群組宣告。
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.component: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 19a8400a076825f17501fabdb3f38ea05915822e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138742"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>設定應用程式的群組宣告與 Azure Active Directory （公開預覽）

Azure Active Directory 所提供的應用程式中使用的權杖中的使用者群組成員資格資訊。  支援兩種主要模式：

- 群組屬性所識別其 Azure Active Directory 物件識別碼 (OID) （正式推出）
- SAMAccountName 或 Active Directory (AD) 同步處理群組和使用者 （公開預覽） 的 GroupSID 屬性所識別的群組

> [!IMPORTANT]
> 有一些要注意這項預覽功能的注意事項：
>
>- 支援使用從內部部署同步處理的 sAMAccountName 和安全性識別碼 (SID) 屬性的設計是要讓移動現有的應用程式從 AD FS 和其他識別提供者。 在 Azure AD 中管理群組不包含發出這些宣告所需的屬性。
>- 在大型組織的使用者群組的成員數目可能會超過 Azure Active Directory 會將新增至權杖的限制。 SAML 權杖中，而 200 代表 JWT 的 150 群組。 這可能會導致無法預期的結果。 如果這是潛在的問題我們建議您測試，並視需要等到我們新增的增強功能可讓您限制應用程式的宣告相關群組。  
>- 新的應用程式開發，或在其中設定應用程式，並不需要巢狀的群組支援的位置的情況下，我們建議您在應用程式授權根據應用程式角色，而不是群組。  這會限制數量能夠移往正確的語彙基元，是更安全，分隔從應用程式設定使用者指派的資訊。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>移轉 AD FS 和其他識別提供者的應用程式的群組宣告

設定為使用 AD FS 進行驗證的許多應用程式依賴 Windows AD 群組屬性的表單中的群組成員資格資訊。   這些屬性是群組 sAMAccountName，可能是完整的網域名稱或 Windows 群組安全性識別元 (GroupSID)。  當使用 AD FS 同盟應用程式時，AD FS 會使用 TokenGroups 函式來擷取使用者的群組成員資格。

為了符合應用程式會接收來自 AD FS 的權杖，以群組和角色宣告，都可能包含網域限定 sAMAccountName，而不是群組的 Azure Active Directory objectID 會發出。

群組宣告支援的格式如下：

- **Azure Active Directory 群組 ObjectId** （適用於所有群組）
- **SAMAccountName** （適用於從 Active Directory 同步處理的群組）
- **NetbiosDomain\sAMAccountName** （適用於從 Active Directory 同步處理的群組）
- **DNSDomainName\sAMAccountName** （適用於從 Active Directory 同步處理的群組）
- **在內部部署群組安全性識別元**（適用於從 Active Directory 同步處理的群組）

> [!NOTE]
> sAMAccountName 與在內部部署群組 SID 屬性才有提供的群組物件從 Active Directory 同步處理。   它們都無法使用在 Azure Active Directory 或 Office365 中建立的群組。   設定 Azure Active Directory 中的應用程式來取得內部同步處理的群組屬性會取得它們只同步處理群組。

## <a name="options-for-applications-to-consume-group-information"></a>使用群組資訊的應用程式的選項

若要取得群組資訊的應用程式的一種方式是呼叫圖形群組端點，以擷取已驗證之使用者的群組成員資格。 此呼叫可確保所有使用者群組的成員可用，即使有大量的群組相關，而應用程式必須要列舉所有使用者群組的成員。  列舉群組然後無關的語彙基元的大小限制。

不過，如果現有的應用程式已預期會使用透過它所收到的權杖中宣告的群組資訊，可以設定 Azure Active Directory 與許多不同的宣告以符合應用程式的需求的選項。  請考量下列選項：

- 使用群組成員資格進行中的應用程式授權時偏好使用的群組 ObjectID，這是不可變和在 Azure Active Directory 中是唯一且可供所有群組。
- 如果使用內部部署群組 sAMAccountName 進行授權，使用完整的網域名稱; 具有較少的情況下，所引發的機會發生名稱衝突。 在它自己的 SAMAccountName 可能內是唯一的 Active Directory 網域，但如果一個以上的 Active Directory 網域同步處理與 Azure Active Directory 租用戶已有相同名稱的多個群組。
- 請考慮使用[應用程式角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)提供一層的群組成員資格和應用程式之間的間接取值。   接著，應用程式會根據 在權杖中的角色 clams 內部的授權決策。
- 如果應用程式設定為從 Active Directory 取得同步處理的群組屬性，而且群組不包含這些屬性則不會在宣告中。
- 在權杖中的群組宣告包含巢狀的群組。   如果使用者屬於 GroupB GroupB 是群組的成員，則會包含使用者的群組宣告，群組和 GroupB。 具有巢狀群組的繁重使用量的組織和有大量的群組成員資格的使用者權杖中所列的群組數目可以成長的語彙基元的大小。   Azure Active Directory 會將它就會發出 SAML 判斷提示，150，而 200 代表以防止權杖變得太大的 JWT 權杖中的群組數目限制。  如果使用者的限制比更大量的群組成員，會發出群組以及 Graph 端點，以取得群組資訊的連結。

> 使用 群組屬性從 Active Directory 同步處理的必要條件： 必須從使用 Azure AD Connect 的 Active Directory 同步處理群組。

有兩個步驟來設定 Azure Active Directory 發出的 Active Directory 群組的群組名稱。

1. **同步處理群組名稱從 Active Directory**之前 Azure Active Directory 可以發出的群組名稱，或在內部部署群組上群組或角色中的 SID 宣告，需要從 Active Directory 同步處理所需的屬性。  您必須執行 Azure AD Connect 1.2.70 版或更新版本。   之前的版本 1.2.70 Azure AD Connect 會同步處理群組物件，從 Active Directory，但預設不包含必要的群組名稱的屬性。  您應該升級至最新版本。

2. **要包含在權杖中的群組宣告的 Azure Active Directory 中設定應用程式註冊**群組宣告可以是設定在資源庫 」 或 「 非資源庫 SAML SSO 應用程式，在入口網站的 [企業應用程式] 區段或應用程式資訊清單區段中使用應用程式註冊。  若要設定此應用程式資訊清單，請參閱下方的 < 設定 Azure Active Directory 應用程式註冊的群組屬性 > 中的群組宣告。

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>設定群組宣告的 SAML 應用程式使用 SSO 組態

若要設定的組件庫或非資源庫 SAML 應用程式的群組宣告，請開啟 企業應用程式、 按一下清單中的應用程式和選取單一登入設定。

選取 「 權杖中傳回群組 」 旁的 [編輯] 圖示

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

若要選取哪些群組應該包含在權杖中使用選項按鈕

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 選取項目 | 描述 |
|----------|-------------|
| **所有群組** | 發出安全性群組和通訊群組清單。   它也會導致將使用者指派要在 'wids' 宣告中發出的目錄角色和使用者指派給要在角色宣告中發出的任何應用程式角色。 |
| **安全性群組** | 會發出安全性使用者群組之成員的群組宣告 |
| **通訊群組清單** | 發出使用者為成員的通訊群組清單 |
| **目錄角色** | 如果使用者被指派目錄角色所發出 'wids' 宣告 （群組宣告時，則不會發出） |

例如，若要發出所有安全性使用者群組的成員，請選取 安全性群組

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

若要發出使用 Active Directory 屬性，而不是 Azure AD Objectid 的從 Active Directory 同步處理群組，請從下拉式清單選取所需的格式。  這會將宣告中的物件識別碼取代包含群組名稱的字串值。   從 Active Directory 同步處理的群組將會包含宣告中。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>進階選項

發出群組宣告的方式可以修改進階選項 下的設定

自訂群組宣告的名稱：如果選取，就可以指定不同的宣告類型的群組宣告。   輸入 名稱 欄位和選擇性的命名空間中命名空間 欄位中的宣告的宣告型別。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些應用程式需要群組成員資格資訊，才會出現在 「 角色 」 宣告。 您可以選擇性地發出使用者的群組做為角色的 '發出的群組宣告的角色' 方塊。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用發出群組資料，以角色身分選項時，只有群組會出現在角色宣告。  將使用者指派的任何應用程式角色不會出現在角色宣告。

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>設定 Azure AD 應用程式的註冊群組屬性

群組宣告也可以設定在[選擇性宣告](../../active-directory/develop/active-directory-optional-claims.md)一節[應用程式資訊清單](../../active-directory/develop/reference-app-manifest.md)。

1. 在入口網站]-> [Azure Active Directory]-> [應用程式註冊]-> [選取應用程式]-> [資訊清單

2. 藉由變更 groupMembershipClaim 讓群組成員資格宣告

   有效值為：

   - [全部]
   - "SecurityGroup"
   - 「 DistributionList"
   - "DirectoryRole"

   例如︰

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   預設群組 Objectid 將發出群組宣告值。  若要修改的宣告值，以包含在內部部署群組屬性，或變更角色宣告類型，使用 OptionalClaims 組態如下所示：

3. 設定群組名稱設定選擇性宣告。

   如果您想要在權杖中的群組以包含選用的宣告區段中的 AD 群組屬性可讓您指定此語彙基元類型的選擇性宣告應該套用到在內部部署、 要求的選擇性宣告和所需的任何其他內容的名稱。  可以列出多個語彙基元的型別：

   - OIDC 識別碼權杖的 idToken
   - accessToken OAuth/OIDC 存取權杖
   - Saml2Token SAML 權杖。

   > [!NOTE]
   > Saml2Token 類型適用於 SAML1.1 和 SAML2.0 格式語彙基元

   針對每個相關的語彙基元型別，修改群組宣告，以使用資訊清單中的 [OptionalClaims] 區段。 OptionalClaims 結構描述如下所示：

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 選擇性宣告結構描述 | Value |
   |----------|-------------|
   | **名稱：** | 必須是 「 群組 」 |
   | **來源：** | 不使用。 省略或指定 null |
   | **重要：** | 不使用。 省略或指定 false |
   | **additionalProperties:** | 其他屬性的清單。  Valid options are "sam_account_name", “dns_domain_and_sam_account_name”, “netbios_domain_and_sam_account_name”, "emit_as_roles" |

   在 additionalProperties 只有其中一個 「 sam_account_name"，"dns_domain_and_sam_account_name"，"netbios_domain_and_sam_account_name 」 則是必要項目。  如果多個存在，則第一個和任何其他人被忽略。

   某些應用程式需要在角色宣告的使用者的群組資訊。  若要變更要從群組宣告的角色宣告的宣告類型，請將"emit_as_roles 」 加入其他屬性。  群組的值將會發出在角色宣告。

   > [!NOTE]
   > 如果 「 emit_as_roles 」 會使用任何應用程式角色可讓您設定將使用者指派將不會出現在角色宣告

### <a name="examples"></a>範例

發出做為 dnsDomainName\SAMAccountName 格式的 OAuth 存取權杖中的群組名稱的群組

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

若要發出群組名稱，以作為 SAML 和 OIDC 識別碼權杖中宣告的角色，netbiosDomain\samAccountName 格式傳回：

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>後續步驟

[什麼是混合式身分識別？](whatis-hybrid-identity.md)