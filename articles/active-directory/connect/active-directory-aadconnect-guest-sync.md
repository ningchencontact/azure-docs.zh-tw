---
title: 同步處理使用電子郵件來登入 Azure 的來賓使用者帳戶 | Microsoft Docs
description: 本文說明如何對使用替代識別碼來登入應用程式的來賓使用者帳戶進行同步處理。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: 4e6cf3bb4a691380a5fe22f5afdf749b40f15ef3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>對使用電子郵件登入的來賓使用者帳戶進行同步處理 (預覽)

>[!NOTE]
> 此功能目前為公開預覽狀態。

如果您的內部部署 AD 環境中可能會有外部使用者 (例如合作夥伴) 使用替代的登入方法，下列案例可針對這種情況做出處理。

在上述範例中，Nina Morin 任職於 Fabrikam，並擁有下列電子郵件地址：nmorin@fabrikam.com。Nina 是 Contoso 的合作夥伴，其需要存取 Contoso 所擁有的某些應用程式。 Contoso 已為 Nina 建立帳戶，並指示 Nina 使用其電子郵件地址來登入應用程式。

對於他們的內部部署應用程式，此案例以往都很適用。 但現在，Contoso 要將這些應用程式移至雲端，並想要讓他們的協力廠商也獲得相同的體驗。

此案例可解決這種情況。


## <a name="pre-requisites-and-assumptions"></a>先決條件與假設
本節包含您在嘗試設定此案例前，所必須了解的一份先決條件與假設清單。

### <a name="pre-requisites"></a>先決條件
- 全域管理員認證，用以設定 Azure AD Connect、驗證網域和設定網域同盟設定
- Azure AD Connect 1.1.524.0 版或更新版本
- 已驗證的網域，用以設定外部使用者的雲端 UPN (範例：bmcontoso.com)。
- 同盟服務，用以驗證您的外部使用者。 如果您使用 AD FS，其必須是 2012 R2 或更新版本
- 機器上需已安裝 MSOL PowerShell v1.1，以驗證同盟設定。 如需詳細資訊，請參閱 [Azure ActiveDirectory (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)。


### <a name="assumptions"></a>假設 
- Azure AD Connect 已成功完成設定和安裝。 如需如何安裝 Azure AD Connect 的資訊，請參閱 [Azure AD Connect 和同盟](active-directory-aadconnectfed-whatis.md)。
本文件假設：
- 您已設定同盟服務，且該服務可成功驗證使用者。
- 外部使用者可使用其外部電子郵件地址進行驗證。
- - 已安裝並設定使用替代識別碼來登入的功能。 使用者可使用其替代識別碼進行驗證。 如需其他有關使用 AD FS 設定替代識別碼的資訊，請參閱[設定替代登入識別碼](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。

## <a name="task-1--prepare-the-environment"></a>工作 1：準備環境
下列工作主要是供您參考，讓您準備好開始同步處理您的外部帳戶，以便他們可以使用替代識別碼 (例如郵件屬性) 來登入。

請先定義下表中的項目，再繼續進行第二項工作。

![架構](media/active-directory-aadconnect-guest-sync/guest2.png)

|環境方面|用途為何？|在您環境中的實作|
|-----|-----|-----|
|雲端 UPN 屬性|此屬性可在雲端中填入外部使用者物件的 UPN。 外部帳戶的 UPN 尾碼必須就是先決條件中所定義的尾碼。 這是已驗證的網域。|* 範例：UserPrincipalName (nmorin@bmcontoso.com)|
|登入地址|外部使用者會在登入時輸入此屬性。 此屬性必須有電子郵件地址格式，且在大部分情況下，會與外部使用者實際的電子郵件地址一致。|* 範例：mail (nmorin@fabrikam.com)|
|Azure AD Connect 限域篩選|此篩選可鎖定外部身分識別，以對本指南稍後所會定義的同步處理規則設定範圍。 一般的設定範圍方式包括：組織中預先定義的 OU、特定的命名慣例、特定網域等等。|* 範例：包含 Externals 的 OU|
|Azure AD 租用戶|Azure AD 租用戶對 Azure AD Connect 顯示的名稱。|範例：contoso.onmicrosoft.com|

下列螢幕擷取畫面中有三個圈住的方框。
- **Externals** OU，用於 Azure AD Connect 限域篩選，而且是外部使用者的位置。
- **mail** 屬性，供外部使用者用來登入。
- **userPrincipalName** 屬性，這是內部部署環境所同盟的已驗證網域。

![User](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>工作 2：設定 Azure AD Connect
定義完上述資訊後，我們可以繼續設定 Azure AD Connect 同步處理規則。 若要設定規則，請使用 Azure AD Connect 同步處理規則編輯器。 如需編輯器的詳細資訊，請參閱[宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)。

### <a name="how-to-configure-the-synchronization-rule"></a>如何設定同步處理規則
使用下列程序來設定 Azure AD Connect。

1. 移至 [開始] - [Azure AD Connect] - [同步處理規則編輯器] 來開啟 Azure AD Connect 同步處理規則編輯器。
2. 在 [同步處理規則編輯器] 畫面上，確定方向是 [輸入]，然後在右側按一下 [新增規則]。
3. 在 [描述] 頁面上設定下列項目，然後按 [下一步]。
    - **名稱** - 輸入規則的名稱 
    - **連接的系統：** - 內部部署 AD 環境
    - **連接的系統物件類型：** - 使用者
    - **Metaverse 物件類型：** - 人員
    - **連結類型：** - 聯結
    - **優先順序：** - 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. 在 [範圍篩選器] 畫面上，按一下 [新增群組]。
5. 使用下拉式清單來設定篩選器。 輸入下列項目，然後按 [下一步]。 這個動作會建立篩選器，並僅套用於位於外部 OU 的物件。
    - **屬性** - dn
    - **運算子** - CONTAINS
    - **值** - Externals
 
 ![Filter](media/active-directory-aadconnect-guest-sync/guest4.png)

6. 在 [聯結規則] 畫面上，按 [下一步]。
7. 在 [轉換] 畫面上，按一下 [新增轉換]。 輸入以下資訊：
    - **FlowType** - 常數
    - **目標屬性** - userType
    - **來源** - 來賓
8. 在 [轉換] 畫面上，按一下 [新增轉換]。 輸入以下資訊：
    - **FlowType** - 直接
    - **目標屬性** - onPremisesUserPrincipalName
    - **來源** - 郵件
9. 在 [轉換] 畫面上，按一下 [新增轉換]。 輸入以下資訊：
    - **FlowType** - 直接
    - **目標屬性** - userPrincipalName
    - **來源** - userPrincipalName ![轉換](media/active-directory-aadconnect-guest-sync/guest5.png)
10. 按一下 [新增] 。 
11. 在 [同步處理規則編輯器] 畫面上，確定方向是 [輸出]，然後在右側按一下 [新增規則]。
12. 在 [描述] 頁面上設定下列項目，然後按 [下一步]。
    - **名稱** - 輸入規則的名稱 
    - **連接的系統：** - Azure AD 租用戶
    - **連接的系統物件類型：** - 使用者
    - **Metaverse 物件類型：** - 人員
    - **連結類型** - 聯結
    - **優先順序：** - 90
    - 
![規則](media/active-directory-aadconnect-guest-sync/guest6.png)

13. 在 [範圍篩選器] 畫面上，按 [下一步]。
14. 在 [聯結規則] 畫面上，按 [下一步]。
15. 在 [轉換] 畫面上，按一下 [新增轉換]。  輸入以下資訊：
    - **FlowType** - 直接
    - **目標屬性** - userType
    - **來源** - userType
9. 在 [轉換] 畫面上，按一下 [新增轉換]。 輸入以下資訊：
    - **FlowType** - 直接
    - **目標屬性** - onPremisesUserPrincipalName
    - **來源** - onPremisesUserPrincipalName ![轉換](media/active-directory-aadconnect-guest-sync/guest7.png)
10. 按一下 [新增] 。 
11. 設定好這些規則後，您需要執行完整的同步處理。 使用 PowerShell 來啟動完整同步處理。 同步處理完成後，您就可以繼續進行下一個步驟。

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>工作 3：同盟
下列工作是要讓您了解必須備妥才能讓案例順利運作的幾項資料。

您可以使用 Azure AD PowerShell 來驗證您的 Azure 同盟設定。 本文件使用 MSOL PowerShell v1.1。 您可以[在此](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)安裝這個版本。

### <a name="verify-the-federation-settings"></a>驗證同盟設定
使用下列程序來驗證同盟設定。
1. 開啟 Windows PowerShell，然後使用下列命令連線至 Azure AD：
``` powershell
      Connect-MSOLservice
```
2.  輸入全域系統管理員認證
3.  現在輸入下列命令：
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   請注意，系統應該會傳回同盟資訊。 請注意 **ActiveLogonUri** 是同盟伺服器的 URL。

  ![同盟](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>驗證替代登入識別碼
本文件使用 AD FS 作為識別提供者 (Idp)。 如果您要使用不同的 Idp，這些步驟可能會不同。

1. 開啟 Windows PowerShell 並輸入下列命令：
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. 您應該會看到 AD FS 資訊。  記下 **AlternateLoginID** 和 **LookupForests**。

![ADFS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>工作 4：測試
為了驗證此案例可正常運作，您需要登入已設定為使用 Idp 進行驗證的端點。 為了測試這一點，我們已在 Azure 中部署網站，並使用下列 url：contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>驗證您可以使用替代識別碼來登入
1. 登入端點。</br>
![端點](media/active-directory-aadconnect-guest-sync/guest10.png)
1. 輸入您的使用者名稱，然後系統會將您重新導向至同盟登入頁面。
![同盟](media/active-directory-aadconnect-guest-sync/guest11.png)
1. 輸入認證。
2. 您現在應該可以順利登入了。
「成功」![](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>後續步驟
- [Azure Active Directory B2B 共同作業使用者的屬性](../../active-directory/b2b/user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [設定替代登入識別碼](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect︰版本發行歷程記錄](active-directory-aadconnect-version-history.md)
