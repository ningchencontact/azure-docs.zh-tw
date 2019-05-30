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
ms.openlocfilehash: 59af4e20c7fe838f7c725b47e45968941fa85cb7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254063"
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

- 預設應用程式註冊入口網站的一項資源 URI-建議您將`api://{clientId}`。 此資源 URI 是唯一的但不是人類可讀取。 您可以將它變更，但請確定它是唯一。
- 一或多個範圍

範圍也會在呈現給使用者使用您的應用程式的同意畫面上顯示。 因此，您必須提供描述範圍的對應字串：

- 終端使用者所見
- 租用戶管理員角色可授與系統管理員同意所見

### <a name="how-to-expose-the-api"></a>如何公開 API

1. 選取 **公開 API**在應用程式註冊中，區段和：
   1. 選取 [新增範圍]  。
   1. 接受建議的應用程式識別碼 URI (api:// {clientId}) 藉由選取**儲存並繼續**。
   1. 輸入下列參數：
      - 針對**領域名稱**，使用`access_as_user`。
      - 針對**誰可以同意**，請確定**系統管理員和使用者**選項。
      - 在 **系統管理員同意顯示名稱**，輸入`Access TodoListService as a user`。
      - 在 **系統管理員同意描述**，輸入`Accesses the TodoListService Web API as a user`。
      - 在 **使用者同意顯示名稱**，輸入`Access TodoListService as a user`。
      - 在 **使用者同意描述**，輸入`Accesses the TodoListService Web API as a user`。
      - 保持**狀態**設為**已啟用**。
      - 選取 **新增範圍**。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的程式碼組態](scenario-protected-web-api-app-configuration.md)
