---
title: 設定適用於 B2B-Azure Active Directory 與 AD FS 的直接同盟 |Microsoft Docs
description: 了解如何設定 AD FS 為直接同盟的身分識別提供者讓來賓可以登入您的 Azure AD 應用程式
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8f709186f0ef17e037c4203118be07ea2d4f511
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544318"
---
# <a name="example-direct-federation-with-active-directory-federation-services-ad-fs-preview"></a>範例：直接同盟與 Active Directory Federation Services (AD FS) （預覽）
|     |
| --- |
| 直接同盟是 Azure Active directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文說明如何設定[直接同盟](direct-federation.md)使用 Active Directory Federation Services (AD FS) 作為 SAML 2.0 或 Ws-fed 身分識別提供者。 若要支援直接聯盟，某些屬性和宣告必須在設定身分識別提供者。 為了說明如何設定直接同盟的身分識別提供者，我們將使用 Active Directory Federation Services (AD FS) 作為範例。 我們將示範如何設定 AD FS 作為 SAML 識別提供者和 Ws-fed 身分識別提供者。

> [!NOTE]
> 本文說明如何設定 AD FS 的 SAML 和 Ws-fed 供說明之用。 針對身分識別提供者的 AD FS 的直接同盟整合，我們建議您使用 Ws-fed 作為通訊協定。 

## <a name="configure-ad-fs-for-saml-20-direct-federation"></a>設定 SAML 2.0 直接同盟的 AD FS
可以設定 azure AD B2B，與下面所列的特定需求使用 SAML 通訊協定的身分識別提供者同盟。 為了說明 SAML 組態步驟，本節會說明如何設定 SAML 2.0 的 AD FS。 

若要設定直接聯盟，必須從身分識別提供者 SAML 2.0 回應中收到下列屬性。 藉由連結到線上的安全性權杖服務的 XML 檔案或手動輸入認證，可以設定這些屬性。 中的步驟 12[建立的測試 AD FS 執行個體](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)描述如何尋找 AD FS 端點或如何產生您的中繼資料 URL，例如`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`。 

|屬性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |簽發者的夥伴 IdP，如範例的 URI `http://www.example.com/exk10l6w90DHM0yi...`         |

設定身分識別提供者所發出的 SAML 2.0 權杖中，需要下列宣告：


|屬性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


下一節會說明如何設定必要的屬性和使用 AD FS 做為範例 SAML 2.0 身分識別提供者的宣告。

### <a name="before-you-begin"></a>開始之前

AD FS 伺服器必須已設定並正常運作之前開始此程序。 如需設定 AD FS 伺服器的說明，請參閱[Azure 虛擬機器上建立的測試 AD FS 3.0 執行個體](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。

### <a name="add-the-claim-description"></a>新增宣告描述

1. 在您的 AD FS 伺服器上選取**工具** > **AD FS 管理**。
2. 在 [導覽] 窗格中，選取**服務** > **宣告描述**。
3. 底下**動作**，選取**新增宣告描述**。
4. 在 [**新增宣告描述**] 視窗中，指定下列值：

   - **顯示名稱**:持續性識別碼
   - **宣告識別項**: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - 選取核取方塊**為可以接受此 federation service 的宣告型別，在同盟中繼資料發行此宣告描述**。
   - 選取核取方塊**同盟中繼資料中發行此宣告的描述，為此同盟服務可傳送的宣告型別**。

5. 按一下 [確定]  。

### <a name="add-the-relying-party-trust-and-claim-rules"></a>新增信賴憑證者信任和宣告規則

1. 在 AD FS 伺服器上，請移至**工具** > **AD FS 管理**。
2. 在 [導覽] 窗格中，選取**信任關係** > **信賴憑證者信任**。
3. 底下**動作**，選取**新增信賴憑證者信任**。 
4. 在 [新增信賴憑證者信任精靈] 的**選取資料來源**，使用選項**相關的信賴憑證者的合作對象的匯入資料發佈到線上或區域網路上**。 指定此同盟中繼資料 URL- https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml 。 保留其他預設選取項目。 選取 [關閉]  。
5. **編輯宣告規則**精靈 隨即開啟。
6. 在 **編輯宣告規則**精靈中，選取**加入規則**。 在 **選擇規則類型**，選取**以宣告方式傳送 LDAP 屬性**。 選取 [下一步]  。
7. 在 **設定宣告規則**，指定下列值： 

   - **宣告規則名稱**:電子郵件宣告規則 
   - **屬性存放區**:Active Directory 
   - **LDAP 屬性**:E-Mail-Addresses 
   - **傳出宣告類型**:電子郵件地址

8. 選取 [完成]  。
9. **編輯宣告規則**視窗會顯示新的規則。 按一下 **[套用]** 。 
10. 按一下 [確定]  。  

### <a name="create-an-email-transform-rule"></a>建立電子郵件轉換規則
1. 移至**編輯宣告規則**然後按一下**加入規則**。 在 **選擇規則類型**，選取**傳輸傳入宣告**，按一下 **下一步**。 
2. 在 **設定宣告規則**，指定下列值： 

   - **宣告規則名稱**:電子郵件轉換規則 
   - **傳入宣告類型**:電子郵件地址 
   - **傳出宣告類型**:名稱識別碼 
   - **輸出名稱識別碼格式**:持續性識別碼 
   - 選取 [傳遞所有宣告值]  。

3. 按一下 [完成]  。 
4. **編輯宣告規則**視窗會顯示新的規則。 按一下 **[套用]** 。 
5. 按一下 [確定]  。 在 AD FS 伺服器現在已設定為使用 SAML 2.0 通訊協定的直接同盟。

## <a name="configure-ad-fs-for-ws-fed-direct-federation"></a>設定 AD FS Ws-fed 直接同盟 
可以設定 azure AD B2B，與下面所列的特定需求使用 Ws-fed 通訊協定的身分識別提供者同盟。 目前，Azure AD 與相容性包括 AD FS 與 Shibboleth 經過測試兩個 Ws-fed 提供者。 在這裡，我們將使用 Active Directory Federation Services (AD FS) 作為 Ws-fed 身分識別提供者的範例。 如需有關建立信賴憑證者合作對象之間的信任 Ws-fed 相容的提供者與 Azure AD 的詳細資訊，請下載 Azure AD 身分識別提供者相容性文件。

若要設定直接聯盟，下列屬性必須是 Ws-fed 在訊息中收到來自識別提供者。 藉由連結到線上的安全性權杖服務的 XML 檔案或手動輸入認證，可以設定這些屬性。 中的步驟 12[建立的測試 AD FS 執行個體](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)描述如何尋找 AD FS 端點或如何產生您的中繼資料 URL，例如`https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`。
 
|屬性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|對象     |`urn:federation:MicrosoftOnline`         |
|簽發者     |簽發者的夥伴 IdP，如範例的 URI `http://www.example.com/exk10l6w90DHM0yi...`         |

必要的 Ws-fed 權杖的 IdP 所簽發的宣告：

|屬性  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

下一節說明如何設定必要的屬性，並使用 AD FS 做為範例 Ws-fed 身分識別提供者的宣告。

### <a name="before-you-begin"></a>開始之前
AD FS 伺服器必須已設定並正常運作之前開始此程序。 如需設定 AD FS 伺服器的說明，請參閱[Azure 虛擬機器上建立的測試 AD FS 3.0 執行個體](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。


### <a name="add-the-relying-party-trust-and-claim-rules"></a>新增信賴憑證者信任和宣告規則 
1. 在 AD FS 伺服器上，請移至**工具** > **AD FS 管理**。 
1. 在 [導覽] 窗格中，選取**信任關係** > **信賴憑證者信任**。 
1. 底下**動作**，選取**新增信賴憑證者信任**。  
1. 在新增信賴信任精靈，如**選取資料來源**，使用選項**相關的信賴憑證者的合作對象的匯入資料發佈到線上或區域網路上**。 指定此同盟中繼資料 URL: `https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`。  保留其他預設選取項目。 選取 [關閉]  。
1. **編輯宣告規則**精靈 隨即開啟。 
1. 在 **編輯宣告規則**精靈中，選取**加入規則**。 在 **選擇規則類型**，選取**使用自訂規則傳送宣告**。 選取 [下一步]  。 
1. 在 **設定宣告規則**，指定下列值：

   - **宣告規則名稱**:問題的不可變識別碼  
   - **自訂規則**: `c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. 選取 [完成]  。 
1. **編輯宣告規則**視窗會顯示新的規則。 按一下 **[套用]** 。  
1. 在同一個**編輯宣告規則**精靈中，選取**加入規則**。 在  **Cohose 規則型別**，選取**以宣告方式傳送 LDAP 屬性**。 選取 [下一步]  。
1. 在 **設定宣告規則**，指定下列值： 

   - **宣告規則名稱**:電子郵件宣告規則  
   - **屬性存放區**:Active Directory  
   - **LDAP 屬性**:E-Mail-Addresses  
   - **傳出宣告類型**:電子郵件地址 

1.  選取 [完成]  。 
1.  **編輯宣告規則**視窗會顯示新的規則。 按一下 **[套用]** 。  
1.  按一下 [確定]  。 在 AD FS 伺服器現在已設定為使用 Ws-fed 的直接同盟。

## <a name="next-steps"></a>後續步驟
接下來，您將會[在 Azure AD 中設定直接聯盟](direct-federation.md#step-2-configure-direct-federation-in-azure-ad)在 Azure AD 入口網站中或使用 PowerShell。 
