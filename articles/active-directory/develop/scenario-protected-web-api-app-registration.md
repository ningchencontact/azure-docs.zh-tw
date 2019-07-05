---
title: 受保護的 web API-應用程式註冊 |Azure
description: 了解如何建立受保護的 web API 與您要註冊應用程式所需的資訊。
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
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536865"
---
# <a name="protected-web-api-app-registration"></a>受保護的 web API:應用程式註冊

這篇文章會說明應用程式註冊的詳細資訊，為受保護的 web api。

請參閱[快速入門：註冊應用程式與 Microsoft 身分識別平台](quickstart-register-app.md)註冊的應用程式的一般步驟。

## <a name="accepted-token-version"></a>接受語彙基元的版本

Microsoft 身分識別平台端點可以發出權杖的兩種： v1.0 權杖和 v2.0 權杖。 如需有關這些語彙基元的詳細資訊，請參閱 <<c0> [ 存取權杖](access-tokens.md)。 接受語彙基元的版本取決於**支援的帳戶類型**您選擇您在建立您的應用程式時：

- 如果值**支援的帳戶類型**是**中任何組織的目錄和個人 Microsoft 帳戶 (例如 Skype、 Xbox、 Outlook.com) 帳戶**，接受語彙基元的版本將會 v2.0。
- 否則，可接受權杖的版本將會 v1.0。

建立應用程式之後，您可以判斷或變更接受語彙基元的版本，遵循下列步驟：

1. 在 Azure 入口網站中，選取您的應用程式，然後選取**資訊清單**應用程式。
2. 在 資訊清單中，搜尋 **"accessTokenAcceptedVersion"** 。 請注意，其值是**2**。 這個屬性會指定 Azure Active Directory (Azure AD) web API 接受 v2.0 權杖。 如果值為**null**，接受語彙基元的版本是 v1.0。
3. 如果您已經變更的語彙基元的版本，請選取**儲存**。

> [!NOTE]
> Web API 會指定它所接受的權杖的版本 （v1.0 或 v2.0）。 當用戶端會要求您的 web API 的權杖，從 Microsoft 身分識別平台 v2.0 端點時，他們會收到指出哪一個版本已接受由 web API 的權杖。

## <a name="no-redirect-uri"></a>未重新導向 URI

Web Api 不需要註冊重新導向 URI，因為使用者不以互動方式登入。

## <a name="expose-an-api"></a>公開 API

另一個設定特定的 web Api 是公開的 API 和公開的範圍。

### <a name="resource-uri-and-scopes"></a>資源 URI 和範圍

範圍通常會採用`resourceURI/scopeName`。 適用於 Microsoft Graph 的範圍具有類似的快速鍵`User.Read`。 此字串是捷徑`https://graph.microsoft.com/user.read`。

應用程式註冊期間，您必須定義這些參數：

- 資源 URI。 根據預設，應用程式註冊入口網站建議，您可以使用`api://{clientId}`。 此資源 URI 是唯一的但不是人類可讀取。 您可以將它變更，但請確定新的值是唯一的。
- 一或多個*範圍*。 (用戶端應用程式，它們將會顯示為*委派的權限*為您的 web API。)
- 一或多個*應用程式角色*。 (用戶端應用程式，它們將會顯示為*應用程式權限*為您的 web API。)

範圍也會呈現給您的應用程式的終端使用者在同意畫面上顯示。 因此，您必須提供描述範圍的對應字串：

- 由使用者所示。
- 租用戶系統管理員所見，使用者可以授與系統管理員同意。

### <a name="exposing-delegated-permissions-scopes"></a>公開委派的權限 （範圍）

1. 選取 **公開 API**應用程式註冊中的區段。
1. 選取 [新增範圍]  。
1. 如果出現提示，請接受建議的應用程式識別碼 URI (`api://{clientId}`) 選取**儲存並繼續**。
1. 輸入這些參數：
      - 針對**領域名稱**，使用**access_as_user**。
      - 針對**誰可以同意**，請確定**系統管理員和使用者**已選取。
      - 在 **系統管理員同意顯示名稱**，輸入**的使用者身分存取 TodoListService**。
      - 在 **系統管理員同意描述**，輸入**存取 TodoListService Web API，以使用者身分**。
      - 在 **使用者同意顯示名稱**，輸入**的使用者身分存取 TodoListService**。
      - 在 **使用者同意描述**，輸入**存取 TodoListService Web API，以使用者身分**。
      - 保持**狀態**設為**已啟用**。
      - 選取 **新增範圍**。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>如果您的 web API 會呼叫精靈應用程式

在本節中，您將了解如何註冊您的受保護的 web API，因此可以由精靈應用程式安全地呼叫。

- 您將需要公開*應用程式權限*。 您會宣告應用程式權限，因為精靈應用程式沒有使用者互動，因此委派的權限徒勞無功的舉動。
- 租用戶系統管理員可以要求 Azure Active Directory (Azure AD) 來發行權杖，您才能存取 web API 的應用程式權限的其中一個已註冊的應用程式的 web API。

#### <a name="exposing-application-permissions-app-roles"></a>公開的應用程式權限 （應用程式角色）

若要公開應用程式權限，您將需要編輯資訊清單。

1. 在應用程式註冊您的應用程式中，選取**資訊清單**。
1. 編輯資訊清單，藉由找出`appRoles`設定並新增一或多個應用程式角色。 角色定義會提供下列的範例 JSON 區塊。 離開`allowedMemberTypes`設定為`"Application"`只。 請確定`id`是唯一的 GUID，且所`displayName`和`value`不包含空格。
1. 儲存資訊清單。

下列範例顯示的內容`appRoles`。 (`id`可以是任何唯一的 GUID。)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>確保 Azure AD 發出權杖，您的 web API，以只允許用戶端

Web API 會檢查應用程式角色。 （這是開發人員最好的公開應用程式權限）。但是，您也可以設定 Azure AD 租用戶系統管理員存取您的 API 核准的應用程式中只發給您的 web API 的權杖。 若要新增此更高的安全性：

1. 在應用程式**概觀**應用程式註冊頁面上，選取您的應用程式的名稱連結**受控應用程式中的本機目錄**。 此欄位的標題可能會被截斷。 例如，您可能會看到**受控應用程式中的...**

   > [!NOTE]
   >
   > 當您選取此連結時，將會移至**企業應用程式概觀**與您的應用程式建立所在的租用戶中的服務主體相關聯的頁面。 您可以使用您的瀏覽器的 [上一頁] 按鈕瀏覽回到 [應用程式註冊] 頁面。

1. 選取 **屬性**頁面**管理**的企業應用程式頁面的區段。
1. 如果您想要允許從特定用戶端存取您的 web API 的 Azure AD 時，設定**需要使用者指派嗎？** 要**是**。

   > [!IMPORTANT]
   >
   > 如果您設定**需要使用者指派嗎？** 要**是**，要求存取權杖的 web API 時，Azure AD 會檢查用戶端應用程式角色指派。 如果用戶端不指派給任何應用程式角色，Azure AD 會傳回錯誤`invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`。
   >
   > 如果您保留**需要使用者指派嗎？** 設為**No**，*當用戶端要求存取權杖以供您的 web API，Azure AD 不會檢查應用程式角色指派*。 任何背景程式用戶端 （也就是任何用戶端使用用戶端認證流程） 可以取得存取權杖的 api，只是藉由指定其對象。 任何應用程式可以存取 API，而不必要求它的權限。 但您的 web API 可以一律上, 一節中所述來確認應用程式已正確的角色 （這由租用戶系統管理員授權）。 API 執行此驗證來驗證存取權杖具有角色宣告，且此宣告的值正確。 (在本例中，值是`access_as_application`。)

1. 選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼組態](scenario-protected-web-api-app-configuration.md)
