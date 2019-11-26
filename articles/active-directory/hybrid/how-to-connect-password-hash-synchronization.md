---
title: 使用 Azure AD Connect 同步來實作密碼雜湊同步處理 | Microsoft Docs
description: 提供有關密碼雜湊同步處理如何運作及如何設定的資訊。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5ca62bc032c12c568e2b8065630dcd8b687513
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483100"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>使用 Azure AD Connect 同步來實作密碼雜湊同步處理
本文提供您所需資訊，以讓您將使用者密碼從內部部署 Active Directory 執行個體同步處理至雲端式 Azure Active Directory (Azure AD) 執行個體。

## <a name="how-password-hash-synchronization-works"></a>密碼雜湊同步處理如何運作
Active Directory 網域服務是以使用者實際密碼的雜湊值表示法格式來儲存密碼。 雜湊值是單向數學函式 (「雜湊演算法」) 的計算結果。 沒有任何方法可將單向函式的結果還原為純文字版本的密碼。 

為了同步密碼，Azure AD Connect 同步處理會從內部部署的 Active Directory 執行個體擷取您的密碼雜湊。 在將密碼雜湊與 Azure Active Directory 驗證服務同步之前，會進行額外的安全性處理。 密碼會以每個使用者為基礎，依照時間先後順序來進行同步處理。

密碼雜湊同步處理程序的實際資料流程，與使用者資料的同步處理類似。 不過，相較於其他屬性的標準目錄同步作業週期，密碼會更頻繁地進行同步。 密碼雜湊同步處理程序每 2 分鐘會執行一次。 您無法修改此程序的執行頻率。 當您同步處理密碼時，它便會覆寫現有的雲端密碼。

第一次啟用密碼雜湊同步處理功能時，它會對範圍內的所有使用者執行初次密碼同步處理。 您無法明確定義一小組要同步處理的使用者密碼。 However, if there are multiple connectors, it is possible to disable password hash sync for some connectors but not others using the [Set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) cmdlet.

當您變更內部部署密碼時，更新後的密碼將會進行同步處理，而這個動作大多會在幾分鐘內完成。
密碼雜湊同步處理功能會自動重試失敗的同步處理嘗試。 若嘗試同步密碼期間發生錯誤，該錯誤會記錄在事件檢視器中。

密碼同步處理不會影響目前已登入的使用者。
在您已經登入雲端服務的情況下，目前的雲端服務工作階段不會立即受到同步處理的密碼變更影響。 不過，當雲端服務要求您重新驗證時，就需要提供新的密碼。

使用者必須輸入其公司認證第二次對 Azure AD 進行驗證，不論他們是否登入其公司網路。 不過，如果使用者在登入時選取 [讓我保持登入 (KMSI)] 核取方塊，則此模式可最小化。 選取此方塊會設定在 180 天內略過驗證的工作階段 Cookie。 KMSI 行為可以由 Azure AD 系統管理員啟用或停用。 此外，您可以藉由開啟[無縫 SSO](how-to-connect-sso.md) 來減少密碼提示，此功能會在使用者是位於連線到您公司網路的公司裝置上時，自動將他們登入。

> [!NOTE]
> 只有 Active Directory 的物件類型使用者才支援密碼同步。 不支援 iNetOrgPerson 物件類型。

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>密碼雜湊同步處理運作方式的詳細描述

下列小節深入說明 Active Directory 與 Azure AD 之間的密碼雜湊同步處理運作方式。

![詳細的密碼流程](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. AD Connect 伺服器上的密碼雜湊同步處理代理程式每隔兩分鐘就會向 DC 要求儲存的密碼雜湊 (unicodePwd 屬性)。  此要求是使用標準 [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) \(英文\)複寫通訊協定，用來在 DC 之間同步資料。 服務帳戶必須具有複寫目錄變更和複寫目錄變更所有 AD 權限 (預設在安裝時授與)，以取得密碼雜湊。
2. 在傳送之前，DC 會使用金鑰 (它是 RPC 工作階段金鑰和 salt 的 [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) 雜湊)，來加密 MD4 密碼雜湊。 然後它會透過 RPC 將結果傳送給密碼雜湊同步處理代理程式。 DC 也會使用 DC 複寫通訊協定將 salt 傳送至同步處理代理程式，讓代理程式可以解密信封。
3. 在密碼雜湊同步處理代理程式將信封加密後，它會使用 [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) 和 salt 來產生金鑰，以將接收的資料解密回其原始 MD4 格式。 密碼雜湊同步處理代理程式永遠不會存取純文字密碼。 密碼雜湊同步處理代理程式使用 MD5 純粹是為了與 DC 的複寫通訊協定相容，並且只會在內部部署環境中的 DC 和密碼雜湊同步處理代理程式之間使用。
4. 密碼雜湊同步處理代理程式會將 16 位元組二進位密碼雜湊擴展成 64 位元組，方法是先將該雜湊轉換成 32 位元組十六進位字串，再將此字串轉換回具有 UTF-16 編碼的二進位檔。
5. 密碼雜湊同步處理代理程式會將每位使用者 salt 處理 (由 10 位元組長度 salt 所組成) 新增至 64 位元組二進位檔，以進一步保護原始雜湊。
6. 接著，密碼雜湊同步處理代理程式會結合 MD4 雜湊加上每位使用者 salt 處理，然後將其輸入至 [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) 函式。 系統會使用 [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) 索引雜湊演算法的 1000 次反覆運算。 
7. 密碼雜湊同步處理代理程式會使用產生的 32 位元組雜湊，將每位使用者 salt 處理和 SHA256 反覆運算數與其串連 (以供 Azure AD 使用)，然後透過 SSL 將字串從 Azure AD Connect 傳輸至 Azure AD。</br> 
8. 當使用者嘗試登入 Azure AD 並且輸入其密碼時，密碼會執行相同的 MD4+salt+PBKDF2+HMAC-SHA256 程序。 如果產生的雜湊符合 Azure AD 中儲存的雜湊，使用者輸入的密碼正確並且通過驗證。

> [!NOTE]
> 系統不會將原始的 MD4 雜湊傳輸至 Azure AD。 而是會傳輸原始 MD4 雜湊的 SHA256 雜湊。 如此一來，如果取得儲存在 Azure AD 的雜湊，它不能在內部部署傳遞雜湊攻擊中使用。

### <a name="security-considerations"></a>安全性考量

同步密碼時，不會向密碼雜湊同步處理功能、Azure AD 或任何相關的服務公開純文字版本的密碼。

使用者驗證是針對 Azure AD 進行，而不是針對組織自己的 Active Directory 執行個體進行。 儲存在 Azure AD 中的 SHA256 密碼資料 (原始 MD4 雜湊的雜湊)，比儲存在 Active Directory 中的資料更安全。 此外，因為此 SHA256 雜湊無法解密，所以無法帶回組織的 Active Directory 環境，並且在傳遞雜湊攻擊中顯示為有效的使用者密碼。

### <a name="password-policy-considerations"></a>密碼原則考量

啟用密碼雜湊同步處理會影響兩種類型的密碼原則：

* 密碼複雜性原則
* 密碼到期原則

#### <a name="password-complexity-policy"></a>密碼複雜性原則

啟用密碼雜湊同步處理時，內部部署 Active Directory 執行個體中的密碼複雜性原則，會覆寫所同步處理使用者在雲端中的複雜性原則。 您可以使用內部部署 Active Directory 執行個體的所有有效密碼，來存取 Azure AD 服務。

> [!NOTE]
> 直接在雲端建立的使用者的密碼仍受制於在雲端定義的密碼原則。

#### <a name="password-expiration-policy"></a>密碼到期原則

If a user is in the scope of password hash synchronization, by default the cloud account password is set to *Never Expire*.

您可以使用內部部署環境中已過期的同步處理密碼，繼續登入雲端服務。 您的雲端密碼會於下一次您在內部部署環境中變更密碼時更新。

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Public preview of the *EnforceCloudPasswordPolicyForPasswordSyncedUsers* feature

If there are synchronized users that only interact with Azure AD integrated services and must also comply with a password expiration policy, you can force them to comply with your Azure AD password expiration policy by enabling the *EnforceCloudPasswordPolicyForPasswordSyncedUsers* feature.

When *EnforceCloudPasswordPolicyForPasswordSyncedUsers* is disabled (which is the default setting), Azure AD Connect sets the PasswordPolicies attribute of synchronized users to "DisablePasswordExpiration". This is done every time a user's password is synchronized and instructs Azure AD to ignore the cloud password expiration policy for that user. You can check the value of the attribute using the Azure AD PowerShell module with the following command:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


To enable the EnforceCloudPasswordPolicyForPasswordSyncedUsers feature, run the following command using the MSOnline PowerShell module:

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers -Enable $true`

Once enabled, Azure AD does not go to each synchronized user to remove the `DisablePasswordExpiration` value from the PasswordPolicies attribute. Instead, the value is set to `None` during the next password sync for each user when they next change their password in on-premises AD.  

It is recommended to enable EnforceCloudPasswordPolicyForPasswordSyncedUsers, prior to enabling password hash sync, so that the initial sync of password hashes does not add the `DisablePasswordExpiration` value to the PasswordPolicies attribute for the users.

The default Azure AD password policy requires users to change their passwords every 90 days. If your policy in AD is also 90 days, the two policies should match. However, if the AD policy is not 90 days, you can update the Azure AD password policy to match by using the Set-MsolPasswordPolicy PowerShell command.

Azure AD supports a separate password expiration policy per registered domain.

Caveat: If there are synchronized accounts that need to have non-expiring passwords in Azure AD, you must explicitly add the `DisablePasswordExpiration` value to the PasswordPolicies attribute of the user object in Azure AD.  You can do this by running the following command.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> This feature is in Public Preview right now.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>Public Preview of synchronizing temporary passwords and "Force Password on Next Logon"

It is typical to force a user to change their password during their first logon, especially after an admin password reset occurs.  It is commonly known as setting a "temporary" password and is completed by checking the "User must change password at next logon" flag on a user object in Active Directory (AD).
  
The temporary password functionality helps to ensure that the transfer of ownership of the credential is completed on first use, to minimize the duration of time in which more than one individual has knowledge of that credential.

To support temporary passwords in Azure AD for synchronized users, you can enable the *ForcePasswordResetOnLogonFeature* feature, by running the following command on your Azure AD Connect server, replacing <AAD Connector Name> with the connector name specific to your environment:

`Set-ADSyncAADCompanyFeature -ConnectorName "<AAD Connector name>" -ForcePasswordResetOnLogonFeature $true`

You can use the following command to determine the connector name:

`(Get-ADSyncConnector | where{$_.ListName -eq "Windows Azure Active Directory (Microsoft)"}).Name`

Caveat:  Forcing a user to change their password on next logon requires a password change at the same time.  AD Connect will not pick up the force password change flag by itself, it is supplemental to the detected password change that occurs during password hash sync.

> [!CAUTION]
> If you do not enable Self-service Password Reset (SSPR) in Azure AD users will have a confusing experience when they reset their password in Azure AD and then attempt to sign in in Active Directory with the new password, as the new password isn’t valid in Active Directory. You should only use this feature when SSPR and Password Writeback is enabled on the tenant.

> [!NOTE]
> This feature is in Public Preview right now.

#### <a name="account-expiration"></a>帳戶到期

如果您的組織使用 accountExpires 屬性作為使用者帳戶管理的一部分，這個屬性不會同步處理至 Azure AD。 如此一來，在針對密碼雜湊同步處理設定之環境中已到期的 Active Directory 帳戶在 Azure AD 將仍然為作用中。 我們的建議是，如果帳戶到期，工作流程動作應觸發 PowerShell 指令碼，以停用使用者的 Azure AD 帳戶 (使用 [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) \(英文\) Cmdlet)。 相反地，當帳戶開啟時，Azure AD 執行個體也應開啟。

### <a name="overwrite-synchronized-passwords"></a>覆寫已同步的密碼

系統管理員可以使用 Windows PowerShell 手動重設您的密碼。

在此情況下，新的密碼會覆寫您已同步處理的密碼，且雲端中定義的所有密碼原則都會套用到新的密碼。

如果您再次變更內部部署密碼，則新密碼會同步到雲端並覆寫手動更新的密碼。

密碼同步處理不會影響已登入的 Azure 使用者。 目前的雲端服務工作階段不會立即受到同步處理的密碼變更之影響，會在您登入雲端服務時才會受到影響。 KMSI 會延伸此差異的持續時間。 當雲端服務要求您重新驗證時，就需要提供新的密碼。

### <a name="additional-advantages"></a>其他優點

- 一般而言，密碼雜湊同步處理比同盟服務更容易實作。 它不需要任何額外的伺服器，並且會排除高可用性同盟服務的相依性以驗證使用者。
- 您也可以除了同盟之外，再啟用密碼雜湊同步處理。 您可以將它作為同盟服務發生中斷時的後援服務。

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Password hash sync process for Azure AD Domain Services

If you use Azure AD Domain Services to provide legacy authentication for applications and services that need to use Keberos, LDAP, or NTLM, some additional processes are part of the password hash synchronization flow. Azure AD Connect uses the additional following process to synchronize password hashes to Azure AD for use in Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect should only be installed and configured for synchronization with on-premises AD DS environments. It's not supported to install Azure AD Connect in an Azure AD DS managed domain to synchronize objects back to Azure AD.
>
> Azure AD Connect only synchronizes legacy password hashes when you enable Azure AD DS for your Azure AD tenant. The following steps aren't used if you only use Azure AD Connect to synchronize an on-premises AD DS environment with Azure AD.
>
> If your legacy applications don't use NTLM authentication or LDAP simple binds, we recommend that you disable NTLM password hash synchronization for Azure AD DS. For more information, see [Disable weak cipher suites and NTLM credential hash synchronization](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect retrieves the public key for the tenant's instance of Azure AD Domain Services.
1. When a user changes their password, the on-premises domain controller stores the result of the password change (hashes) in two attributes:
    * *unicodePwd* for the NTLM password hash.
    * *supplementalCredentials* for the Kerberos password hash.
1. Azure AD Connect detects password changes through the directory replication channel (attribute changes needing to replicate to other domain controllers).
1. For each user whose password has changed, Azure AD Connect performs the following steps:
    * Generates a random AES 256-bit symmetric key.
    * Generates a random initialization vector needed for the first round of encryption.
    * Extracts Kerberos password hashes from the *supplementalCredentials* attributes.
    * Checks the Azure AD Domain Services security configuration *SyncNtlmPasswords* setting.
        * If this setting is disabled, generates a random, high-entropy NTLM hash (different from the user's password). This hash is then combined with the exacted Kerberos password hashes from the *supplementalCrendetials* attribute into one data structure.
        * If enabled, combines the value of the *unicodePwd* attribute with the extracted Kerberos password hashes from the *supplementalCredentials* attribute into one data structure.
    * Encrypts the single data structure using the AES symmetric key.
    * Encrypts the AES symmetric key using the tenant's Azure AD Domain Services public key.
1. Azure AD Connect transmits the encrypted AES symmetric key, the encrypted data structure containing the password hashes, and the initialization vector to Azure AD.
1. Azure AD stores the encrypted AES symmetric key, the encrypted data structure, and the initialization vector for the user.
1. Azure AD pushes the encrypted AES symmetric key, the encrypted data structure, and the initialization vector using an internal synchronization mechanism over an encrypted HTTP session to Azure AD Domain Services.
1. Azure AD Domain Services retrieves the private key for the tenant's instance from Azure Key vault.
1. For each encrypted set of data (representing a single user's password change), Azure AD Domain Services then performs the following steps:
    * Uses its private key to decrypt the AES symmetric key.
    * Uses the AES symmetric key with the initialization vector to decrypt the encrypted data structure that contains the password hashes.
    * Writes the Kerberos password hashes it receives to the Azure AD Domain Services domain controller. The hashes are saved into the user object's *supplementalCredentials* attribute that is encrypted to the Azure AD Domain Services domain controller's public key.
    * Azure AD Domain Services writes the NTLM password hash it received to the Azure AD Domain Services domain controller. The hash is saved into the user object's *unicodePwd* attribute that is encrypted to the Azure AD Domain Services domain controller's public key.

## <a name="enable-password-hash-synchronization"></a>啟用密碼雜湊同步處理

>[!IMPORTANT]
>如果您要從 AD FS (或其他同盟技術) 遷移至密碼雜湊同步處理，強烈建議您遵循我們在[此處](https://aka.ms/adfstophsdpdownload) \(英文\) 發佈的詳細部署指南。

當您使用 [快速設定] 選項來安裝 Azure AD Connect 時，系統會自動啟用密碼雜湊同步處理。 如需詳細資訊，請參閱[利用快速設定開始使用 Azure AD Connect](how-to-connect-install-express.md)。

如果您在安裝 Azure AD Connect 時使用自訂設定，在使用者登入頁面上便會提供密碼雜湊同步處理。 如需詳細資訊，請參閱[自訂 Azure AD Connect 安裝](how-to-connect-install-custom.md)。

![啟用密碼雜湊同步處理](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>密碼雜湊同步處理和 FIPS
如果我們已經根據美國聯邦資訊處理標準 (FIPS) 鎖定您的伺服器，則 MD5 會停用。

**若要針對密碼雜湊同步處理啟用 MD5，請執行下列步驟︰**

1. 移至 %programfiles%\Azure AD Sync\Bin。
2. 開啟 miiserver.exe.config。
3. 移至檔案結尾處的 configuration/runtime 節點。
4. 新增下列節點︰ `<enforceFIPSPolicy enabled="false"/>`
5. 儲存您的變更。

如需參考，此程式碼片段就是其大致樣貌︰

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

如需安全性和 FIPS 的詳細資訊，請參閱 [Azure AD 密碼雜湊同步、加密和 FIPS 合規性](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)。

## <a name="troubleshoot-password-hash-synchronization"></a>針對密碼雜湊同步處理進行疑難排解
如果您在進行密碼雜湊同步處理時發生問題，請參閱[針對密碼雜湊同步處理進行疑難排解](tshoot-connect-password-hash-synchronization.md)。

## <a name="next-steps"></a>後續步驟
* [Azure AD Connect 同步處理：自訂同步處理選項](how-to-connect-sync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)
* [取得從 ADFS 遷移至密碼雜湊同步處理的逐步部署方案](https://aka.ms/authenticationDeploymentPlan)
