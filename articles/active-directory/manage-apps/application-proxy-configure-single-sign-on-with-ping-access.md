---
title: 標頭型驗證搭配 Azure AD 應用程式 Proxy 的 PingAccess | Microsoft Docs
description: 使用 PingAccess 與應用程式 Proxy 來發行應用程式可支援標頭型驗證。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab08c93662988655154cf300ac4ee3758fbc7872
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472810"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>使用應用程式 Proxy 與 PingAccess 的單一登入之標頭式驗證

Azure Active Directory (Azure AD) 應用程式 Proxy 已與 PingAccess 中合作，使您的 Azure AD 客戶可以存取多個應用程式。 PingAccess 會展開[現有應用程式 Proxy 供應項目](application-proxy.md)以包含單一登入存取使用標頭進行驗證的應用程式。

## <a name="whats-pingaccess-for-azure-ad"></a>什麼是 Azure ad 的 PingAccess？

使用 Azure AD 的 PingAccess，您可以使用標頭進行驗證的應用程式提供使用者存取和單一登入 (SSO)。 應用程式 Proxy 會如同任何其他應用程式一樣處理這些應用程式，使用 Azure AD 驗證存取，然後透過連接器服務傳遞流量。 PingAccess 位於前方的應用程式，並從 Azure AD 存取權杖轉譯為標頭。 接著，應用程式會收到驗證，它可以讀取的格式。

使用者在登入使用您公司的應用程式時，將不會注意到什麼不同。 這些還是可以在任何裝置上從任何地方運作。 應用程式 Proxy 連接器會將所有的應用程式，而不考慮其驗證類型，遠端流量，因此它們仍會自動平衡負載。

## <a name="how-do-i-get-access"></a>如何取得存取權？

因為此案例會來自 Azure Active Directory 和 PingAccess 之間的合作關係，您會需要這兩項服務的授權。 不過，Azure Active Directory Premium 訂用帳戶所包含的基本 PingAccess 授權最多可涵蓋 20 個應用程式。 如果您需要發佈 20 個以上的標頭應用程式，可以從 PingAccess 購買額外的授權。

如需詳細資訊，請參閱 [Azure Active Directory 版本](../fundamentals/active-directory-whatis.md)。

## <a name="publish-your-application-in-azure"></a>在 Azure 中發佈應用程式

本文適用於發行第一次在此案例中的應用程式的人員。 除了詳述發佈的步驟，它會引導您開始使用應用程式 Proxy 和 PingAccess。 如果您已經設定這兩項服務，但想要發佈步驟上的重新整理程式，請跳至[新增您的應用程式至 Azure AD 應用程式 Proxy](#add-your-application-to-azure-ad-with-application-proxy)一節。

> [!NOTE]
> 因為此案例是 Azure AD 和 PingAccess 之間的合作關係，有些指示存在於 Ping 身分識別站台。

### <a name="install-an-application-proxy-connector"></a>安裝應用程式 Proxy 連接器

如果您已啟用應用程式 Proxy 啟用，而且已安裝連接器，您可以略過本節並移至[新增您的應用程式至 Azure AD 應用程式 Proxy](#add-your-application-to-azure-ad-with-application-proxy)。

應用程式 Proxy 連接器是 Windows Server 服務，將引導您已發佈的應用程式到您的遠端員工的流量。 如需詳細安裝指示，請參閱[教學課程：Azure Active Directory 中新增透過應用程式 Proxy 遠端存取內部部署應用程式](application-proxy-add-on-premises-application.md)。

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)身為應用程式系統管理員。 **Azure Active Directory 系統管理中心**頁面隨即出現。
2. 選取  **Azure Active Directory** > **應用程式 proxy** > **下載連接器服務**。 **應用程式 Proxy 連接器下載**頁面隨即出現。

   ![應用程式 proxy 連接器下載](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)
3. 請遵循安裝指示。

下載連接器應會自動會啟用為您的目錄的應用程式 Proxy，但如果沒有，您可以選取**啟用應用程式 Proxy**。

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>新增您的應用程式至 Azure AD 應用程式 Proxy

您需要在 Azure 入口網站中採取兩個動作。 首先，您必須使用應用程式 Proxy 發佈應用程式。 然後，您需要收集一些關於您可以在 PingAccess 步驟期間使用的應用程式資訊。

#### <a name="publish-your-application"></a>發佈您的應用程式

您必須先發佈您的應用程式。 這個動作牽涉到：

- 新增您的內部部署應用程式，Azure ad
- 將使用者指派的測試應用程式，然後選擇 標頭為基礎的 SSO
- 設定應用程式的重新導向 URL
- 授與的權限的使用者和其他應用程式使用您的內部部署應用程式

若要發佈您自己的內部部署應用程式：

1. 如果您未在最後一節中，登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)身為應用程式系統管理員。
2. 選取 **企業應用程式** > **新的應用程式** > **在內部部署應用程式**。 **新增您自己的內部部署應用程式**頁面隨即出現。

   ![新增您自己的內部部署應用程式](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
3. 填寫必要的欄位，新的應用程式的相關資訊。 使用以下的指導方針設定。

   > [!NOTE]
   > 此步驟的更詳細的逐步解說，請參閱[新增至 Azure AD 的內部部署應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。

   1. **內部 URL**：通常您會提供帶您前往應用程式的登入頁面，在公司網路時的 URL。 此案例中，連接器需要將 PingAccess proxy 視為應用程式的首頁。 使用此格式︰`https://<host name of your PingAccess server>:<port>`。 連接埠預設為 3000，但您可以在 PingAccess 中設定它。

      > [!WARNING]
      > 此類型的單一登入，必須使用內部 URL`https`且無法使用`http`。

   2. **預先驗證方法**：選擇**Azure Active Directory**。
   3. **轉譯標頭中的 URL**：選擇**No**。

   > [!NOTE]
   > 如果這是您的第一個應用程式，請在變更 PingAccess 設定時，使用連接埠 3000 啟動並返回以更新這項設定。 對於後續的應用程式，連接埠必須符合您已在 PingAccess 中設定的接聽程式。 深入了解 [PingAccess 中的接聽程式](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)。
4. 選取 [新增]  。 新的應用程式的 [概觀] 頁面隨即出現。

現在將指派的使用者應用程式測試，然後選擇標頭式單一登入：

1. 從應用程式資訊看板中，選取**使用者和群組** > **新增使用者** > **使用者和群組 (\<數字 > 選取)** . 使用者和群組的清單時，會出現您選擇的。

   ![使用者和群組](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)
2. 選取的使用者應用程式測試，然後選取**選取**。 請確定此測試帳戶可存取內部部署應用程式。
3. 選取 [指派]  。
4. 從應用程式資訊看板中，選取**單一登入** > **標頭型**。

   > [!TIP]
   > 如果這是您第一次使用標頭式單一登入，您需要安裝 PingAccess。 若要確定您的 Azure 訂用帳戶會與 PingAccess 安裝自動產生關聯，請使用此單一登入頁面上的連結來下載 PingAccess。 您現在可以開啟下載網站，或稍後返回此頁面。

   ![標頭型登入](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)
5. 選取 [ **儲存**]。

請確定您的重新導向 URL 設定為您的外部 URL:

1. 從**Azure Active Directory 系統管理中心**提要欄位中，選取**Azure Active Directory** > **應用程式註冊**。 應用程式清單隨即出現。

   ![應用程式註冊](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)
2. 選取您的應用程式。
3. 選取的連結旁邊**重新導向 Uri**，顯示的 Uri 設定適用於 web 和公開用戶端的重新導向數目。 **\<應用程式名稱 >-驗證**頁面隨即出現。
4. 檢查是否在您指派給您的應用程式中稍早的外部 URL**重新導向 Uri**清單。 若非如此，將新增的外部 URL，使用的重新導向 URI 類型**Web**，然後選取**儲存**。

最後，設定您的內部部署應用程式，可讓使用者有讀取權限，其他應用程式可讀取/寫入存取：

1. 從**應用程式註冊**資訊看板以取得您的應用程式中，選取**API 的權限** > **新增權限** >  **Microsoft Api** > **Microsoft Graph**。 **要求的 API 權限**頁，以查看**Microsoft Graph**隨即出現，其中包含 Api 的 Windows Azure Active Directory。

   ![要求 API 權限](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)
2. 選取 **委派的權限** > **使用者** > **User.Read**。
3. 選取 **應用程式權限** > **應用程式** > **Application.ReadWrite.All**。
4. 選取 **新增權限**。
5. 在  **API 權限**頁面上，選取**授與系統管理員同意\<您的目錄名稱 >** 。

#### <a name="collect-information-for-the-pingaccess-steps"></a>收集 PingAccess 步驟的資訊

您需要收集下列三項資訊來設定您的應用程式，使用 PingAccess 的 (所有 Guid):

| Azure AD 的欄位名稱 | PingAccess 欄位名稱 | 資料格式 |
| --- | --- | --- |
| **應用程式 （用戶端） 識別碼** | **用戶端識別碼** | GUID |
| **目錄 （租用戶） 識別碼** | **簽發者** | GUID |
| `PingAccess key` | **用戶端祕密** | 隨機字串 |

若要收集此資訊：

1. 從**Azure Active Directory 系統管理中心**提要欄位中，選取**Azure Active Directory** > **應用程式註冊**。 應用程式清單隨即出現。
2. 選取您的應用程式。 **應用程式註冊**頁面會顯示您的應用程式。

   ![註冊應用程式的概觀](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)
3. 旁**應用程式 （用戶端） 識別碼**值，選取**複製到剪貼簿**圖示，然後複製並儲存它。 您指定此值稍後為 PingAccess 的用戶端識別碼。
4. 下一步**目錄 （租用戶） 識別碼**值，也選取**複製到剪貼簿**，然後複製並儲存它。 您指定此值稍後為 PingAccess 的簽發者。
5. 從資訊看板**應用程式註冊**針對您的應用程式中，選取**憑證和祕密** > **新的用戶端祕密**。 **新增用戶端祕密**頁面隨即出現。

   ![新增用戶端祕密](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)
6. 在 **描述**，輸入`PingAccess key`。
7. 底下**Expires**，選擇如何設定 PingAccess 機碼：**在 1 年**， **2 年內**，或**永不**。
8. 選取 [新增]  。 PingAccess 機碼會顯示在用戶端祕密，資料表中使用的隨機字串中的，會自動**值**欄位。
9. PingAccess 金鑰旁邊**值**欄位中，選取**複製到剪貼簿**圖示，然後複製並儲存它。 您指定此值稍後為 PingAccess 的用戶端祕密。

### <a name="update-graphapi-to-send-custom-fields-optional"></a>更新 GraphAPI 以傳送自訂欄位 （選擇性）

如果您需要傳送其他權杖內的自訂宣告 PingAccess 所耗用的 access_token，設定`acceptMappedClaims`應用程式欄位`True`。 您可以使用 Graph Explorer 或 Azure AD 入口網站的應用程式資訊清單，以進行這項變更。

**此範例使用 Graph Explorer:**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**這個範例會使用[Azure Active Directory 入口網站](https://aad.portal.azure.com/)更新`acceptMappedClaims`欄位：**

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)身為應用程式系統管理員。
2. 選取 [Azure Active Directory]   > [應用程式註冊]  。 應用程式清單隨即出現。
3. 選取您的應用程式。
4. 從資訊看板**應用程式註冊**應用程式頁面，選取**資訊清單**。 您的應用程式註冊資訊清單的 JSON 程式碼會出現。
5. 搜尋`acceptMappedClaims`欄位，並將值變更為`True`。
6. 選取 [ **儲存**]。


### <a name="use-of-optional-claims-optional"></a>使用的選擇性宣告 （選擇性）
選擇性宣告可讓您新增的每個使用者和租用戶都 standard-but-not-included-by-default 宣告。 您可以藉由修改應用程式資訊清單來設定您的應用程式的選擇性宣告。 如需詳細資訊，請參閱[了解 Azure AD 應用程式資訊清單的文件](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)

將電子郵件地址包含 access_token PingAccess 將取用的範例：
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>使用的宣告對應原則 （選用）
[宣告對應原則 （預覽）](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties/)的屬性不存在於 azure Ad。 宣告對應可讓您藉由新增額外的自訂宣告您的 ADFS 或使用者物件所支援將舊的內部部屬應用程式移轉至雲端

若要讓應用程式使用自訂宣告，並且包含其他欄位，請確定您已經也[建立自訂宣告對應原則並將它指派給應用程式](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

> [!NOTE]
> 若要使用自訂宣告，您必須已定義自訂原則且已指派給應用程式。 此原則應包含所有必要的自訂屬性。
>
> 您可以執行原則定義和透過 PowerShell、 Azure AD Graph 總管 中或 Microsoft Graph 的指派。 如果您要在 PowerShell 中執行它們，您可能需要先使用`New-AzureADPolicy`，然後將它指派給應用程式與`Add-AzureADServicePrincipalPolicy`。 如需詳細資訊，請參閱 <<c0> [ 宣告對應原則指派](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)。

範例：
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id 
```

### <a name="enable-pingaccess-to-use-custom-claims-optional-but-required-if-you-expect-the-application-to-consume-additional-claims"></a>啟用使用 （選用，但會需要您所預期的應用程式使用額外的宣告） 的自訂宣告的 PingAccess
當您將設定 PingAccess 下一個步驟中時，Web 工作階段您將建立 (設定]-> [存取]-> [Web 工作階段) 必須擁有**要求的設定檔**取消選取此選項並**重新整理使用者屬性**若要設定**否**

## <a name="download-pingaccess-and-configure-your-application"></a>下載 PingAccess 及設定您的應用程式

現在您已完成所有的 Azure Active Directory 安裝步驟，可以移至設定 PingAccess。

此案例的 PingAccess 部分詳細的步驟會繼續 Ping 身分識別文件中。 請依照下列中的指示[設定來保護使用 Microsoft Azure AD 應用程式 Proxy 發佈的應用程式的 Azure AD 的 PingAccess](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) Ping Identity 網站上。

這些步驟可協助您安裝 PingAccess 及設定 PingAccess 帳戶 （如果沒有的話）。 然後，若要建立 Azure AD OpenID Connect (OIDC) 連線，您設定使用的權杖提供者**目錄 （租用戶） 識別碼**您從 Azure AD 入口網站複製的值。 接下來，若要在 PingAccess 上建立 web 工作階段，您使用**應用程式 （用戶端） 識別碼**和`PingAccess key`值。 之後，您可以設定身分識別對應，並建立虛擬主機、網站和應用程式。

### <a name="test-your-application"></a>測試您的應用程式

當您完成所有這些步驟之後時，您的應用程式應該啟動並執行。 若要測試它，開啟瀏覽器並瀏覽至您在 Azure 中發佈應用程式時所建立的外部 URL。 使用您指派給應用程式的測試帳戶登入。

## <a name="next-steps"></a>後續步驟

- [設定來保護使用 Microsoft Azure AD 應用程式 Proxy 發佈的應用程式的 Azure AD 的 PingAccess](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Azure Active Directory 中的應用程式單一登入](what-is-single-sign-on.md)
- [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](application-proxy-troubleshoot.md)
