---
title: "設定登入時自動物線加速應用程式使用的主領域探索原則 |Microsoft 文件"
description: "說明哪些 Azure AD 租用戶，以及如何管理 Azure 透過 Azure Active Directory。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: e2e6e5c40dc4a9f67f94c45f8394512db3f777f5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>設定登入時自動物線加速應用程式使用的主領域探索原則

下列文件提供主領域探索和自動加速的簡介。

## <a name="home-realm-discovery"></a>主領域探索
首頁領域探索 (HRD) 是可讓 Azure Active Directory (Azure AD) 若要判斷，程序在登入時，使用者需要驗證。  當使用者登入時，若要存取資源時，Azure AD 租用戶或 Azure AD 常見登入頁面時，他們輸入使用者名稱 (UPN)。 Azure AD 會使用該值來探索使用者需要登入。 

使用者可能需要採取的下列位置的其中一個來進行驗證：

- 使用者 （可能是相同的租用戶的使用者嘗試存取的資源） 的主要租用戶。 

- Microsoft 帳戶。  使用者是 guest 資源租用戶中。

- 另一個身分識別提供者同盟與 Azure AD 租用戶。

-  例如，Active Directory Federation Services (AD FS) 在內部部署身分識別提供者。

## <a name="auto-acceleration"></a>自動加速 
某些組織中設定其與另一個 IdP，例如使用者驗證的 AD FS 同盟的 Azure Active Directory 租用戶。  

這些情況下，當使用者符號至應用程式中，第一次第一次出現與 Azure AD 登入頁面。 輸入其 UPN 之後，它們會進入 IdP 登入頁面。 在某些情況下，系統管理員可能想要將使用者導向至登入頁面時要登入特定的應用程式。 

這表示使用者可以略過初始的 Azure Active Directory 頁面。 此程序稱為 「 登入時自動物線加速。 」

在另一個登入 IdP 至同盟租用戶時的情況下，自動加速可讓使用者登入變得更有效率。  您可以為個別的應用程式設定自動加速。

>[!NOTE]
>如果您設定應用程式以自動加速，來賓使用者無法登入。 如果您直接到同盟 IdP 以供驗證使用者，沒有任何方法，才能回到 Azure Active Directory 登入頁面。 Guest 使用者，可能需要被導向至其他租用戶或外部的 IdP，例如 Microsoft 帳戶，無法登入該應用程式因為它們正在略過主領域探索步驟。  

有兩種方式，以控制自動物線加速至同盟 IdP:   

- 使用應用程式驗證要求網域提示。 
- 設定主領域探索原則來啟用自動加速。

## <a name="domain-hints"></a>網域提示 
網域提示是從應用程式的驗證要求中包含的指示詞。 它們可用來加速至其同盟 IdP 登入頁面的使用者。 由多租用戶應用程式以加速直接到加上品牌的使用者或 Azure AD 登入頁面為租用戶。  

例如，"largeapp.com 」 應用程式可能會讓他們的客戶在存取應用程式自訂 URL 」 contoso.largeapp.com。 」 應用程式可能也會加入 contoso.com 網域提示在驗證要求。 

網域提示語法會使用的通訊協定而異，它通常在應用程式中設定。

**WS-同盟**: whr=contoso.com 查詢字串中的。

**SAML**： 任一個的 SAML 驗證要求會包含網域提示或查詢字串 whr=contoso.com。

**開啟連接識別碼**： 查詢字串 domain_hint=contoso.com。 

如果網域提示會包含在驗證要求，從應用程式，並與該網域同盟租用戶，Azure AD 會嘗試以設定該網域的 IdP 登入重新導向。 

如果網域提示未參考的已驗證的同盟網域，則會忽略它，並叫用標準的主領域探索。

如需自動加速使用支援的 Azure Active Directory 網域提示的詳細資訊，請參閱[Enterprise Mobility + Security 部落格](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)。

>[!NOTE]
>如果網域提示包含在驗證要求，其目前狀態會覆寫任何 HRD 原則設定應用程式。

## <a name="home-realm-discovery-policy"></a>主領域探索原則
某些應用程式不會提供設定它們所發出驗證要求的方法。 在這些情況下，不可能使用網域提示來控制自動加速。 您可以透過原則設定自動加速來達成相同的行為。  

### <a name="set-hrd-policy"></a>設定 HRD 原則
有三個步驟來設定登入時自動物線加速應用程式上：


1. 建立自動加速 HRD 原則。

2. 尋找要連接原則的服務主體。

3. 將原則附加至服務主體。 原則可能建立在租用戶，但是沒有任何作用直到它們附加到實體。 

HRD 原則可以附加至為服務主體時，而且只有一個 HRD 原則可以在給定的實體上作用中一次。  

您可以使用 Microsoft Azure Active Directory Graph API 直接管理或 Azure Active Directory PowerShell cmdlet 來設定自動加速使用 HRD 原則。

Graph API 來管理原則所述[原則上的作業](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)MSDN 上的發行項。

以下是範例 HRD 原則定義：
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

原則類型是"HomeRealmDiscoveryPolicy。 」

如果**AccelerateToFederatedDomain**為 false，原則沒有任何作用。

**PreferredDomain**應指出要以加速網域。 如果租用戶的只有一個同盟的網域可以省略它。  如果省略，而且沒有多個驗證同盟的網域，原則就會有任何作用。

如果**PreferredDomain**指定時，它必須符合租用戶的已驗證的同盟網域。 所有使用者的應用程式都必須能夠登入該網域。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD 原則的優先順序和評估
您可以建立 HRD 原則，並將其指派給特定的組織及服務主體。 這意謂著可以將多個原則套用至某個特定的應用程式。 生效的 HRD 原則會遵循下列規則：


- 如果網域提示出現在驗證要求，則會忽略任何 HRD 原則。 會使用網域提示所指定的行為。

- 否則，如果已將原則明確指派給服務主體，就會強制執行該原則。 

- 如果沒有任何網域提示，而且沒有原則已明確指派給服務主體，會強制執行已明確指派給父組織的服務主體的原則。 

- 如果沒有網域提示，且未指派原則給服務主體或組織，則會使用預設 HRD 行為。

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>使用 HRD 原則，將登入自動物線加速設定應用程式上的教學課程
我們將使用 Azure AD PowerShell cmdlet 來逐步完成幾個案例，包括：


- 設定應用程式的自動加速在租用戶與單一的同盟網域。

- 設定自動物線加速您的租用戶已驗證的數個網域的其中一個應用程式。

- 列出原則設定的應用程式。

### <a name="prerequisites"></a>必要條件
在下列範例中，您建立、 更新連結，並在 Azure AD 中刪除應用程式服務主體上的原則。

1.  若要開始，請下載最新的 Azure AD PowerShell cmdlet 預覽。 

2.  下載 Azure AD PowerShell cmdlet 之後，執行連接命令，來登入與您的系統管理員帳戶的 Azure AD:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  執行下列命令，請參閱您的組織中的所有原則：

    ``` powershell
    Get-AzureADPolicy
    ```

如果沒有傳回任何內容，這表示您沒有任何租用戶中建立的原則。

### <a name="example-set-auto-acceleration-for-an-application"></a>範例： 設定應用程式的自動加速 
在此範例中，您可以建立自動-加速使用者到 AD FS 登入畫面時所登入應用程式的原則。 使用者可以登入 AD FS 而不必先在 Azure AD 登入頁面上輸入的使用者名稱。 

#### <a name="step-1-create-an-hrd-policy"></a>步驟 1：建立 HRD 原則
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

如果您有單一的同盟的網域，以便驗證使用者對應用程式，您需要建立一個 HRD 原則。  

若要查看新的原則，並取得其**ObjectID**，執行下列命令：

``` powershell
Get-AzureADPolicy
```


若要啟用自動加速，您有 HRD 原則之後，您可以將它指派給多個應用程式服務的原則。

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>步驟 2： 找出要用來將原則指派的服務主體  
您需要**ObjectID**您要將原則指派服務主體。 有數種方法來尋找**ObjectID**服務主體。    

您可以使用入口網站中，或者您可以查詢[Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity)。 您也可以移至[圖表總管工具](https://graphexplorer.cloudapp.net/)和登入您的 Azure AD 帳戶以查看貴組織的所有服務主體。 因為您使用 PowerShell，您可以使用 get AzureADServicePrincipal 指令程式來列出服務原則及其 Id。

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>步驟 3：將原則指派給服務主體  
之後**ObjectID**您要設定自動加速應用程式的服務主體，執行下列命令。 此命令會將您在步驟 1 中建立與您在步驟 2 中找到的服務主體的 HRD 原則產生關聯。

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

您可以重複此命令，針對您要將原則新增的每個服務主體。

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>步驟 4：檢查您的自動加速原則指派給哪些應用程式服務主體
若要檢查哪些應用程式有設定自動加速原則，使用**Get AzureADPolicyAppliedObject** cmdlet。 將它傳遞**ObjectID**您想要檢查的原則。

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>步驟 5：大功告成！
要檢查新原則是否正常運作，請嘗試應用程式。

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>範例： 列出為設定自動加速原則的應用程式

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>步驟 1： 列出已建立您組織中的所有原則 

``` powershell
Get-AzureADPolicy
```

請注意**ObjectID**您想要的清單中指派的原則。

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>步驟 2： 將此原則會指派服務主體  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>範例： 移除應用程式的自動加速原則
#### <a name="step-1-get-the-objectid"></a>步驟 1： 取得 ObjectID
使用上述範例來取得**ObjectID**的原則，以及您要將它移除，應用程式服務主體。 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>步驟 2： 移除原則指派從應用程式服務主體  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>步驟 3： 檢查移除列出的服務主體，此原則會指派 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>後續步驟
- 如需有關如何驗證 Azure AD 中的運作方式的詳細資訊，請參閱[Azure AD 的驗證案例](develop/active-directory-authentication-scenarios.md)。
- 如需使用者單一登入的詳細資訊，請參閱[應用程式存取和單一登入與 Azure Active Directory](active-directory-enterprise-apps-manage-sso.md)。
- 如需所有開發人員相關內容的概觀，請瀏覽 [Active Directory 開發人員指南](develop/active-directory-developers-guide.md)。
