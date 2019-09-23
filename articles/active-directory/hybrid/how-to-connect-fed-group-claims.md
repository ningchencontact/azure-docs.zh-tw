---
title: 使用 Azure Active Directory 設定應用程式的群組宣告 |Microsoft Docs
description: 有關如何設定群組宣告以與 Azure AD 搭配使用的資訊。
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170479"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>使用 Azure Active Directory 設定應用程式的群組宣告（公開預覽）

Azure Active Directory 可以在權杖中提供使用者群組成員資格資訊，以便在應用程式中使用。  支援兩種主要模式：

- 依其 Azure Active Directory 物件識別元（OID）屬性所識別的群組（已正式運作）
- Active Directory （AD）同步處理的群組和使用者的 sAMAccountName 或 GroupSID 屬性所識別的群組（公開預覽）

> [!IMPORTANT]
> 針對此預覽功能，有一些需要注意的事項：
>
>- 支援使用從內部部署同步處理的 sAMAccountName 和安全識別碼（SID）屬性，其設計目的是要讓現有應用程式從 AD FS 和其他身分識別提供者移動。 Azure AD 中受管理的群組不包含發出這些宣告所需的屬性。
>- 在較大型組織中，使用者所屬的群組數目可能會超過 Azure Active Directory 將新增至權杖的限制。 150適用于 SAML 權杖的群組，以及200的 JWT。 這可能會導致無法預期的結果。 如果這是潛在的問題，我們建議您進行測試，並在必要時等待，直到我們新增增強功能，讓您可以將宣告限制為應用程式的相關群組。  
>- 針對新的應用程式開發，或在可以為其設定應用程式的情況下，以及不需要的嵌套群組支援，我們建議以應用程式角色（而非群組）為基礎的應用程式內授權。  這會限制需要進入權杖、較安全，以及將使用者指派與應用程式設定分開的資訊量。

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>從 AD FS 和其他身分識別提供者遷移之應用程式的群組宣告

許多設定為使用 AD FS 進行驗證的應用程式，都是以 Windows AD 群組屬性的形式，依賴群組成員資格資訊。   這些屬性是群組 sAMAccountName，可能是以功能變數名稱或 Windows 群組安全識別碼（GroupSID）來限定。  當應用程式與 AD FS 同盟時，AD FS 會使用 TokenGroups 函式來取得使用者的群組成員資格。

為了符合應用程式從 AD FS 接收的權杖，可能會發出群組和角色宣告，其中包含網域限定的 sAMAccountName，而不是群組的 Azure Active Directory objectID。

群組宣告支援的格式如下：

- **Azure Active Directory 群組 ObjectId**（適用于所有群組）
- **sAMAccountName**（適用于從 Active Directory 同步處理的群組）
- **NetbiosDomain\sAMAccountName**（適用于從 Active Directory 同步處理的群組）
- **DNSDomainName\sAMAccountName**（適用于從 Active Directory 同步處理的群組）
- **內部部署群組安全識別碼**（適用于從 Active Directory 同步處理的群組）

> [!NOTE]
> sAMAccountName 和內部部署群組 SID 屬性僅適用于從 Active Directory 同步處理的群組物件。   它們無法在 Azure Active Directory 或 Office365 中建立的群組上使用。   在 Azure Active Directory 中設定來取得同步內部部署群組屬性的應用程式，只會取得同步處理的群組。

## <a name="options-for-applications-to-consume-group-information"></a>應用程式使用群組資訊的選項

應用程式若要取得群組資訊，其中一種方式是呼叫圖形群組端點，以取得已驗證使用者的群組成員資格。 此呼叫可確保使用者所屬的所有群組都可供使用，即使牽涉到大量群組，而且應用程式需要列舉使用者所屬的所有群組。  然後，群組列舉會與權杖大小限制無關。

不過，如果現有的應用程式預期會透過其收到的權杖中的宣告來取用群組資訊，Azure Active Directory 可以使用數個不同的宣告選項來設定，以符合應用程式的需求。  請考量下列選項：

- 使用群組成員資格進行應用程式內授權時，最好使用群組 ObjectID，這在 Azure Active Directory 中是不可變且唯一的，而且適用于所有群組。
- 如果使用內部部署群組 sAMAccountName 進行授權，請使用網域限定名稱; 不太可能發生名稱衝突的情況。 sAMAccountName 本身在 Active Directory 網域中可能是唯一的，但如果有一個以上的 Active Directory 網域與 Azure Active Directory 的租使用者同步處理，則可能有多個群組具有相同的名稱。
- 請考慮使用[應用程式角色](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md)來提供群組成員資格和應用程式之間的間接取值層。   然後，應用程式會根據權杖中的角色 clams，進行內部授權決策。
- 如果應用程式已設定為取得從 Active Directory 同步處理的群組屬性，而且群組未包含這些屬性，則宣告中不會包含該屬性。
- 權杖中的群組宣告包含嵌套的群組。   如果使用者是 GroupB 的成員，而 GroupB 是 GroupA 的成員，則使用者的群組宣告將同時包含 GroupA 和 GroupB。 對於大量使用嵌套群組和具有大量群組成員資格之使用者的組織而言，權杖中列出的群組數目可能會增加權杖大小。   Azure Active Directory 會限制針對 SAML 判斷提示，將會在權杖中發出為150的群組數目，以及針對 JWT 使用200來防止權杖變得太大。  如果使用者是數量超過限制的群組成員，則會發出群組，並附上圖形端點的連結以取得群組資訊。

> 使用從 Active Directory 同步處理群組屬性的必要條件： 群組必須使用 Azure AD Connect 從 Active Directory 同步處理。

有兩個步驟可設定 Azure Active Directory 來發出 Active Directory 群組的組名。

1. **從 Active Directory 同步處理組名**在 Azure Active Directory 可以在群組或角色宣告中發出組名或內部部署群組 SID 之前，需要從 Active Directory 同步處理必要的屬性。  您必須執行 Azure AD Connect 1.2.70 或更新版本。   在版本1.2.70 之前 Azure AD Connect 會從 Active Directory 同步處理群組物件，但預設不會包含必要的組名屬性。  您應該升級到目前的版本。

2. **在 Azure Active Directory 中設定應用程式註冊，以在權杖中包含群組宣告**您可以在入口網站的 [企業應用程式] 區段中，針對資源庫或非資源庫的 SAML SSO 應用程式設定群組宣告，或在 [應用程式註冊] 區段中使用應用程式資訊清單。  若要在應用程式資訊清單中設定群組宣告，請參閱下面的「設定群組屬性的 Azure Active Directory 應用程式註冊」。

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>使用 SSO 設定為 SAML 應用程式設定群組宣告

若要設定資源庫或非資源庫 SAML 應用程式的群組宣告，請開啟 [企業應用程式]，按一下清單中的應用程式，然後選取 [單一登入設定]。

選取 [權杖中傳回的群組] 旁邊的 [編輯] 圖示

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

使用選項按鈕來選取要包含在權杖中的群組

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 選取範圍 | 描述 |
|----------|-------------|
| **所有群組** | 發出安全性群組和通訊群組清單。   它也會導致指派給使用者的目錄角色在 ' wids ' 宣告中發出，而指派給使用者的任何應用程式角色會在角色宣告中發出。 |
| **安全性群組** | 在群組宣告中發出使用者所屬的安全性群組 |
| **通訊群組清單** | 發出使用者所屬的通訊群組清單 |
| **目錄角色** | 如果使用者被指派目錄角色，則會將其發出為 ' wids ' 宣告（不會發出群組宣告） |

例如，若要發出使用者所屬的所有安全性群組，請選取 [安全性群組]

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

若要使用同步自 Active Directory 的 Active Directory 屬性來發出群組，而不是 Azure AD Objectid，請從下拉式選單選取所需的格式。  這會將宣告中的物件識別碼取代為包含組名的字串值。   只有從 Active Directory 同步處理的群組才會包含在宣告中。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>進階選項

群組宣告的發出方式可由 [Advanced options] 底下的設定來修改。

自訂群組宣告的名稱：如果選取此選項，則可以為群組宣告指定不同的宣告類型。   在 [名稱] 欄位中輸入宣告類型，並在 [命名空間] 欄位中輸入宣告的選擇性命名空間。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

某些應用程式需要群組成員資格資訊才會出現在「角色」宣告中。 您可以藉由勾選 [發出群組角色宣告] 方塊，選擇性地將使用者群組當做角色來發出。

![宣告 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 如果使用 [將群組資料發出為角色] 選項，則只有群組會出現在角色宣告中。  指派給使用者的任何應用程式角色都不會出現在角色宣告中。

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>設定群組屬性的 Azure AD 應用程式註冊

群組宣告也可以在[應用程式資訊清單](../../active-directory/develop/reference-app-manifest.md)的[選擇性宣告](../../active-directory/develop/active-directory-optional-claims.md)區段中設定。

1. 在入口網站中 > Azure Active Directory-> 應用程式註冊-> 選取 [應用程式-> 資訊清單]

2. 藉由變更 groupMembershipClaim 來啟用群組成員資格宣告

   有效的值為：

   - 這
   - "SecurityGroup"
   - DistributionList
   - DirectoryRole

   例如:

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   根據預設，會在群組宣告值中發出群組 Objectid。  若要將宣告值修改為包含內部部署群組屬性，或將宣告類型變更為角色，請使用 OptionalClaims 設定，如下所示：

3. 設定組名 configuration 選擇性宣告。

   如果您想要權杖中的群組包含 [選擇性宣告] 區段中的內部部署 AD 群組屬性，請指定應套用哪一個權杖類型的選擇性宣告、所要求的選擇性宣告的名稱，以及所需的任何其他屬性。  可以列出多個權杖類型：

   - OIDC 識別碼權杖的 idToken
   - OAuth/OIDC 存取權杖的 accessToken
   - SAML 權杖的 Saml2Token。

   > [!NOTE]
   > Saml2Token 類型同時適用于 SAML 1.1 和 SAML 2.0 格式權杖

   針對每個相關的權杖類型，修改群組宣告以使用資訊清單中的 OptionalClaims 區段。 OptionalClaims 架構如下所示：

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 選擇性宣告架構 | 值 |
   |----------|-------------|
   | **檔案名** | 必須是「群組」 |
   | **來源** | 未使用。 省略或指定 null |
   | **基本** | 未使用。 省略或指定 false |
   | **AdditionalProperties** | 其他屬性的清單。  有效的選項為 "sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"、"emit_as_roles" |

   在 additionalProperties 中，只需要 "sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name" 其中之一。  如果有多個，則會使用第一個，並忽略其他任何專案。

   某些應用程式需要角色宣告中的使用者群組資訊。  若要將宣告類型從群組宣告變更為角色宣告，請將 "emit_as_roles" 新增至其他屬性。  群組值會在角色宣告中發出。

   > [!NOTE]
   > 如果使用 "emit_as_roles"，則設定為使用者指派的任何應用程式角色都不會出現在角色宣告中。

### <a name="examples"></a>範例

以 dnsDomainName\SAMAccountName 格式在 OAuth 存取權杖中以組名的形式發出群組

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

若要以 netbiosDomain\samAccountName 格式發出要傳回的組名作為 SAML 和 OIDC 識別碼權杖中的角色宣告：

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
