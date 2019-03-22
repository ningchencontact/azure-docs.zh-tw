---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "58115572"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在 [管理] 底下，選取 [使用者流程]，並按一下 [+新增使用者流程]。

![選取新的使用者流程](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

在 [建議] 索引標籤上，選取 [註冊並登入]。

![選取註冊並登入使用者流程](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

輸入使用者流程 [名稱]，以供您的應用程式參考。 例如，輸入 `SiUpIn`。

在 [識別提供者] 底下，勾選 [電子郵件註冊]。 (選擇性) 您也可以選取社交身分識別提供者 (如果已經設定)。

在 [多重要素驗證] 底下，選擇 [已啟用] 或 [已停用]。

![以識別提供者身分輸入名稱並選取 [電子郵件註冊]](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

在 [使用者屬性和宣告] 底下，選取 [顯示更多]，即可查看可供選擇的屬性和宣告的完整清單。

在 [收集屬性] 欄中，選擇註冊期間要向取用者收集的屬性。 例如，勾選 [國家/區域]、[顯示名稱] 和 [郵遞區號]。

在 [傳回宣告] 欄中，選擇成功註冊或登入後，您要在授權權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱]、[識別提供者]、[郵遞區號]、[使用者是新的] 和 [使用者的物件識別碼]。

按一下 [確定]。

![選取部分使用者屬性和宣告後，按一下 [確定] 按鈕](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

按一下 [建立]  新增使用者流程。 使用者流程會列示為 **B2C_1_SiUpIn**。 名稱會附加 **B2C_1_** 前置詞。

選取 [執行使用者流程]。 驗證資料表中指定的設定，然後按一下 [執行使用者流程]。

![選取 [執行使用者流程]](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| 設定      | 值  |
| ------------ | ------ |
| **應用程式** | Contoso B2C 應用程式 |
| **回覆 URL** | `https://localhost:44316/` |

新的瀏覽器索引標籤隨即開啟，您可以依照設定驗證註冊或登入取用者體驗。

> [!NOTE]
> 建立和更新使用者流程後，需要經過一分鐘才會生效。
>