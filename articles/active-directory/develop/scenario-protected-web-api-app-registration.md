---
title: 受保護的 web API-應用程式註冊 |Azure
description: 了解如何建立受保護的 Web API 與您要註冊應用程式所需的資訊。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111119"
---
# <a name="protected-web-api---app-registration"></a>受保護的 web API-應用程式註冊

這篇文章說明受保護的 web API 的應用程式註冊細節。

請參閱[快速入門：註冊應用程式與 Microsoft 身分識別平台](quickstart-register-app.md)如需如何註冊應用程式的一般步驟。

## <a name="accepted-token-version"></a>接受語彙基元的版本

Microsoft 身分識別平台端點可以發出權杖的兩種： v1.0 權杖和 v2.0 權杖。 您可以深入了解在這些語彙基元[存取權杖](access-tokens.md)。 接受語彙基元的版本取決於**支援的帳戶類型**您選擇您在建立您的應用程式時：

- 如果值**支援的帳戶類型**是**中任何組織的目錄和個人 Microsoft 帳戶 (例如 Skype、 Xbox、 Outlook.com) 帳戶**，接受語彙基元的版本將會 v2.0。
- 否則，可接受權杖的版本將會 v1.0。

一旦您已建立應用程式，您可以依照下列步驟來變更接受語彙基元的版本：

1. 在 Azure 入口網站中，選取您的應用程式，然後選取**資訊清單**應用程式。
2. 在 資訊清單中，搜尋 **"accessTokenAcceptedVersion 」** ，並查看其值是**2**。 這個屬性可讓 Azure AD 知道 web API 接受 v2.0 權杖。 如果它是**null**，接受語彙基元的版本將會 v1.0。
3. 選取 [ **儲存**]。

> [!NOTE]
> 它是由 web API，以決定它所接受的權杖的版本 （v1.0 或 v2.0） 時。 當用戶端會要求您的 web API 使用 Microsoft 身分識別平台 v2.0 端點的權杖時，他們會收到指出哪一個版本已接受由 web API 的權杖。

## <a name="no-redirect-uri"></a>未重新導向 URI

Web Api 不需要任何使用者身分登入以互動方式註冊的重新導向 URI。

## <a name="expose-an-api"></a>公開 API

另一個設定特定的 web Api 是公開的 API 和公開的範圍。

### <a name="resource-uri-and-scopes"></a>資源 URI 和範圍

範圍通常是在表單的`resourceURI/scopeName`。 適用於 Microsoft Graph 的範圍具有類似的快速鍵`User.Read`，但此字串是只針對捷徑`https://graph.microsoft.com/user.read`。

應用程式註冊期間，您必須定義下列參數：

- 預設應用程式註冊入口網站的資源 URI-建議您將`api://{clientId}`。 此資源 URI 是唯一的但不是人類可讀取。 您可以將它變更，但請確定它是唯一。
- 一或多個**領域**(用戶端應用程式，它們會顯示為**委派的權限**為您的 Web API)
- 一或多個**應用程式角色**(用戶端應用程式，它們會顯示為**應用程式權限**為您的 Web API)

範圍也會在呈現給終端使用者使用您的應用程式的同意畫面上顯示。 因此，您必須提供描述範圍的對應字串：

- 終端使用者所見
- 租用戶管理員角色可授與系統管理員同意所見

### <a name="how-to-expose-delegated-permissions-scopes"></a>如何公開 （expose） 委派的權限 （範圍）

1. 選取 **公開 API**在應用程式註冊中，區段和：
   1. 選取 [新增範圍]  。
   1. 如果要求，請接受建議的應用程式識別碼 URI (api:// {clientId}) 藉由選取**儲存並繼續**。
   1. 輸入下列參數：
      - 針對**領域名稱**，使用`access_as_user`。
      - 針對**誰可以同意**，請確定**系統管理員和使用者**選項。
      - 在 **系統管理員同意顯示名稱**，輸入`Access TodoListService as a user`。
      - 在 **系統管理員同意描述**，輸入`Accesses the TodoListService Web API as a user`。
      - 在 **使用者同意顯示名稱**，輸入`Access TodoListService as a user`。
      - 在 **使用者同意描述**，輸入`Accesses the TodoListService Web API as a user`。
      - 保持**狀態**設為**已啟用**。
      - 選取 **新增範圍**。

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>其中精靈應用程式會呼叫您的 Web API 的案例

在這段中，您將了解如何註冊將受保護的 Web API，讓它由精靈應用程式可以安全地呼叫：

- 您將需要公開**應用程式權限**。 精靈應用程式不會與使用者互動，並因此委派的權限就沒有意義，只會宣告應用程式權限。
- 租用戶系統管理員可能需要以簽發權杖的 Azure AD，您只想要存取的 Web API 應用程式權限的其中一個已註冊的應用程式的 Web 應用程式。

#### <a name="how-to-expose-application-permissions-app-roles"></a>如何公開應用程式權限 （應用程式角色）

若要公開應用程式權限，您將需要編輯資訊清單。

1. 在應用程式註冊您的應用程式中，按一下**資訊清單**。
1. 編輯資訊清單，藉由找出`appRoles`設定並新增一或多個應用程式角色。 下列範例 JSON 區塊中提供的角色定義。  保留`allowedMemberTypes`只的 「 應用程式 」。 請確定**識別碼**是唯一的 guid 並**displayName**並**值**不包含任何空格。
1. 儲存資訊清單。

內容`appRoles`應該是如下所示 (`id`可以是任何唯一的 GUID)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>如何確保，Azure AD 發出權杖的 Web API 只允許用戶端

Web API 會檢查應用程式角色 （也就是開發人員的方式這麼做）。 但是，您甚至可以設定 Azure Active Directory，以針對您的 Web API 發出的權杖，才能核准的租用戶系統管理員，以存取您的 API 應用程式。 若要新增此額外的安全性：

1. 在應用程式**概觀**應用程式註冊頁面上，選取超連結您的應用程式的名稱取代**受控應用程式中的本機目錄**。 此欄位的標題可能會被截斷。 您可以比方說，讀取： `Managed application in ...`

   > [!NOTE]
   >
   > 當您選取此連結會瀏覽至**企業應用程式概觀**與您的應用程式建立所在的租用戶中的服務主體相關聯的頁面。 您可以使用您的瀏覽器的 [上一頁] 按鈕瀏覽回到 [應用程式註冊] 頁面。

1. 選取 **屬性**頁面**管理**區段的企業應用程式頁面
1. 如果您想要強制執行從特定用戶端存取您的 Web API 的 AAD，設定**需要使用者指派嗎？** 要**是**。

   > [!IMPORTANT]
   >
   > 藉由設定**需要使用者指派嗎？** 要**是**，Web api 要求存取權杖時，AAD 會檢查用戶端應用程式角色指派。 如果用戶端不會指派給任何 AppRoles，AAD 會只會傳回下列錯誤： `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > 如果您保留**需要使用者指派嗎？** 要**No**，<span style='background-color:yellow; display:inline'>當用戶端要求存取權杖為您的 Web API，Azure AD 不會檢查應用程式角色指派</span>。 因此，任何精靈用戶端 （也就是使用用戶端認證流程的任何用戶端） 仍然能夠取得存取權杖的 api，只是藉由指定其對象。 任何應用程式，能夠存取 API，而不必要求它的權限。 現在，這並非然後結束，因為您的 Web API 可以一律下, 一節所述來確認應用程式已正確的角色 （其已獲授權的租用戶系統管理員） 來驗證存取權杖已`roles`宣告和 t 的正確值他的宣告 (在我們的案例`access_as_application`)。

1. 選取 [儲存]  。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼組態](scenario-protected-web-api-app-configuration.md)
