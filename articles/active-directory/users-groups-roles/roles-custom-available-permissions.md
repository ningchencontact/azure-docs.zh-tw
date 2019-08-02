---
title: 應用程式註冊管理的自訂系統管理員角色許可權-Azure Active Directory |Microsoft Docs
description: 委派身分識別管理的自訂系統管理員角色許可權。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ff6755f1391ff19e65df669fb51967a904f4f
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707561"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure Active Directory 中的應用程式註冊子類型和許可權

本文包含 Azure Active Directory (Azure AD) 中自訂角色定義目前可用的應用程式註冊許可權。

## <a name="single-tenant-v-multi-tenant-permissions"></a>單一租使用者 v。 多租使用者許可權

單一租使用者和多租使用者應用程式的自訂角色許可權不同。 單一租使用者應用程式僅適用于已註冊應用程式之 Azure AD 組織中的使用者。 所有 Azure AD 組織都可以使用多租使用者應用程式。 單一租使用者應用程式的定義, 是將**支援的帳戶類型**設定為 [僅限此組織目錄中的帳戶]。 在圖形 API 中, 單一租使用者應用程式會將 signInAudience 屬性設定為 "AzureADMyOrg"。

## <a name="application-registration-subtypes-and-permissions"></a>應用程式註冊子類型和許可權

如需一般詞彙子類型、許可權和屬性集平均值的說明, 請參閱[自訂角色總覽](roles-custom-overview.md)。 下列是應用程式註冊特有的資訊。

### <a name="subtypes"></a>子類型

只有一個應用程式註冊子類型-[myOrganization]。 例如, microsoft 目錄/myOrganization/basic/update。 此子類型設定于特定應用程式註冊的**驗證**頁面上, 並且對應至使用圖形 API 或 PowerShell 將 signInAudience 屬性設定為 "AzureADMyOrg"。 子類型會限制只有組織中的帳戶 (單一租使用者應用程式) 標示為可存取之應用程式註冊的許可權。

您可以使用受限制的許可權, 將讀取或管理許可權授與給內部應用程式, 而不需要將讀取或管理許可權授與其他組織中的帳戶可存取的應用程式。

有 myOrganization 版本的所有讀取和更新許可權, 以及刪除許可權。 目前沒有任何應用程式。建立的 myOrganization 版本。 標準許可權 (例如, microsoft 目錄/應用程式/基本/更新) 會授與所有應用程式註冊類型的讀取或管理許可權。

![宣告單一租使用者應用程式或多租使用者應用程式](./media/roles-custom-available-permissions/supported-account-types.png)

如需自訂角色預覽的下列許可權詳細資料, 請[Azure Active Directory 中的可用自訂角色許可權](roles-custom-available-permissions.md)中列出。

### <a name="create-and-delete"></a>建立和刪除

有兩種許可權可以授與建立應用程式註冊的能力:

- **microsoft 目錄/應用程式/createAsOwner**
- **microsoft 目錄/應用程式/建立**

如果同時指派這兩個許可權, 將會優先使用 create 許可權。 雖然 createAsOwner 許可權不會自動新增 creator 做為第一個擁有者, 但在使用圖形 Api 或 PowerShell Cmdlet 時, 可以在建立應用程式註冊期間指定擁有者。

建立許可權授與**新註冊**命令的存取權。

[這些許可權會授與新註冊入口網站命令的存取權](./media/roles-create-custom/new-custom-role.png)

有兩個許可權可授與刪除應用程式註冊的能力:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

授與刪除應用程式註冊的能力, 而不考慮子類型。也就是單一租使用者和多租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft 目錄/應用程式. myOrganization/delete

授與刪除應用程式註冊的能力, 限制只有您的組織或單一租使用者應用程式 (myOrganization 子類型) 中的帳戶才能存取。

![這些許可權會授與 [刪除應用程式註冊] 命令的存取權](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 指派包含建立許可權的角色時, 必須在目錄範圍中建立角色指派。 在資源範圍指派的 create 許可權不會授與建立應用程式註冊的能力。

### <a name="read"></a>閱讀

根據預設, 組織中的所有成員使用者都可以讀取應用程式註冊資訊。 不過, 來賓使用者和應用程式服務主體則不能。 如果您打算將角色指派給來賓使用者或應用程式, 您必須包含適當的 [讀取] 許可權。

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft 目錄/應用程式/allProperties/讀取

能夠讀取敏感性屬性以外的單一租使用者和多租使用者應用程式的所有屬性 (例如認證)。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>myOrganization/allProperties/read

授與的許可權與 microsoft 目錄/應用程式/allProperties/讀取相同, 但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>microsoft 目錄/應用程式/標準/讀取:授與應用程式注冊商標頁面上所有欄位的存取權

![此許可權可授與應用程式注冊商標頁面的存取權](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>myOrganization/standard/read

授與 microsoft 的相同許可權。目錄/應用程式/標準/讀取, 但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft 目錄/應用程式/擁有者/讀取

授與讀取單一租使用者和多租使用者應用程式上擁有者屬性的能力。 授與 [應用程式註冊擁有者] 頁面上所有欄位的存取權:

![此許可權可授與應用程式註冊擁有者頁面的存取權](./media/roles-custom-available-permissions/app-registration-owners.png)

授與應用程式實體上下列屬性的存取權:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppCreatedDateTime
- AppData
- AppId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- 顯示名稱
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- Mainlogo.png
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>Update

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft 目錄/應用程式/allProperties/更新

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>myOrganization/allProperties/update

授與與 microsoft 目錄/應用程式/allProperties/更新相同的許可權, 但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

授與應用程式註冊驗證頁面上所有欄位的存取權:

![此許可權會授與應用程式註冊驗證頁面的存取權](./media/roles-custom-available-permissions/supported-account-types.png)

授與應用程式資源上下列屬性的存取權:

- AvailableToOtherTenants
- SignInAudience

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft 目錄/應用程式 myOrganization/物件/更新

授與 microsoft 目錄/應用程式/物件/更新的相同許可權, 但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

能夠在單一租使用者和多租使用者應用程式上更新回復 URL、登出 URL、隱含流程和發行者網域屬性。 除了支援的帳戶類型以外, 授與應用程式註冊驗證頁面上所有欄位的存取權:

![授與應用程式註冊驗證的存取權, 但不支援帳戶類型](./media/roles-custom-available-permissions/supported-account-types.png)

 授與應用程式資源上下列屬性的存取權:

- AcceptMappedClaims
- AccessTokenAcceptedVersion
- 項
- GroupMembershipClaims
- IsDeviceOnlyAuthSupported
- OAuth2LegacyUrlPathMatching
- OauthOidcResponsePolicyBitmap
- OptionalClaims
- OrgRestrictions
- PublicClient
- UseCustomTokenSigningKey

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft 目錄/應用程式. myOrganization/驗證/更新

授與的許可權與 microsoft 目錄/應用程式/驗證/更新相同, 但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

能夠在單一租使用者和多租使用者應用程式上更新名稱、標誌、首頁 URL、服務條款 URL 及隱私權聲明 URL 屬性。 授與應用程式注冊商標頁面上所有欄位的存取權:

![此許可權可授與應用程式注冊商標頁面的存取權](./media/roles-custom-available-permissions/app-registration-branding.png)

授與應用程式資源上下列屬性的存取權:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppData
- AppId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- 顯示名稱
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- Mainlogo.png
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft 目錄/應用程式 myOrganization/基本/更新

授與的許可權與 microsoft 目錄/應用程式/基本/更新相同, 但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

能夠在單一租使用者和多租使用者應用程式上更新憑證和用戶端秘密屬性。 授與應用程式註冊憑證 & 秘密頁面上所有欄位的存取權:

![此許可權會授與應用程式註冊憑證 & 秘密頁面的存取權](./media/roles-custom-available-permissions/app-registration-secrets.png)

授與應用程式資源上下列屬性的存取權:
- AsymmetricKey
- EncryptedSecretKey
- KeyDescription
- SharedKeyReference
- TokenEncryptionKeyId

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>myOrganization/認證/更新

會授與 microsoft 目錄/應用程式/認證/更新相同的許可權, 但僅適用于單一目錄應用程式。

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

能夠在單一租使用者和多租使用者上更新擁有者屬性。 授與 [應用程式註冊擁有者] 頁面上所有欄位的存取權:

![此許可權可授與應用程式註冊擁有者頁面的存取權](./media/roles-custom-available-permissions/app-registration-owners.png)

授與應用程式資源上下列屬性的存取權:
- 擁有者

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>myOrganization/擁有者/更新

授與的許可權與 microsoft 目錄/應用程式/擁有者/更新相同, 但僅適用于單一租使用者應用程式。

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

能夠更新委派的許可權、應用程式許可權、授權的用戶端應用程式、所需的許可權, 以及授與單一租使用者和多租使用者應用程式的同意屬性。 並未授與執行同意的能力。 授與應用程式註冊 API 許可權上所有欄位的存取權, 並公開 API 頁面:

![此許可權可授與應用程式註冊 API 許可權頁面的存取權](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![此許可權會授與應用程式註冊公開 API 頁面的存取權](./media/roles-custom-available-permissions/app-registration-expose-api.png)

授與應用程式資源上下列屬性的存取權:

- AppIdentifierUri
- 權利
- PreAuthorizedApplications
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>myOrganization/許可權/更新

授與 microsoft 目錄/應用程式/許可權/更新的相同許可權, 但僅適用于單一租使用者應用程式。

## <a name="required-license-plan"></a>必要的授權方案

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 使用[Azure 入口網站、Azure AD PowerShell 和圖形 API](roles-create-custom.md)來建立自訂角色
- [查看自訂角色的指派](roles-view-assignments.md)
