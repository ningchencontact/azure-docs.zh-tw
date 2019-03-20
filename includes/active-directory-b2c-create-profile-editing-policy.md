---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "58115157"
---
如果您要在您的應用程式中啟用設定檔編輯功能，您可使用**設定檔編輯**使用者流程。 此使用者流程描述客戶在設定檔編輯期間將會經歷的體驗，以及成功完成時，應用程式將收到的權杖內容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在 [管理] 底下，選取 [使用者流程]，並按一下 [+新增使用者流程]。

![選取新的使用者流程](media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

在 [建議] 索引標籤上，選取 [設定檔編輯]。

輸入使用者流程 [名稱]，以供您的應用程式參考。 例如，輸入 `SiPe`。

在 [識別提供者] 底下，勾選 [本機帳戶登入]。 (選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。

![以識別提供者身分來選取 [本機帳戶登入]，然後按一下 [確定] 按鈕](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

在 [使用者屬性] 底下，按一下 [顯示更多]。 在 [收集屬性] 欄中，選擇取用者可以在其設定檔中檢視及編輯的屬性。 例如，勾選 [國家/區域]、[顯示名稱] 和 [郵遞區號]。

在 [傳回宣告] 欄中，選擇成功編輯設定檔後，您要在授權權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱] 和 [郵遞區號]。

按一下 [確定]。

![選取某些應用程式宣告，然後按一下 [確定] 按鈕](media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

按一下 [建立]  新增使用者流程。 使用者流程列示為 **B2C_1_SiPe**。 名稱會附加 **B2C_1_** 前置詞。

選取 [執行使用者流程]。 驗證資料表中指定的設定，然後按一下 [執行使用者流程]。

![選取並執行使用者流程](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| 設定      | 值  |
| ------------ | ------ |
| **應用程式** | Contoso B2C 應用程式 |
| **回覆 URL** | `https://localhost:44316/` |

新的瀏覽器索引標籤隨即開啟，您可以依照設定驗證設定檔編輯取用者體驗。

> [!NOTE]
> 建立和更新使用者流程後，需要經過一分鐘才會生效。
>