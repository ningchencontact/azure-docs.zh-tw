---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456022"
---
如果您只想在您的應用程式中啟用登入功能，您可以使用**登入**使用者流程。 此使用者流程描述客戶在登入期間將經歷的體驗，以及成功登入時，應用程式將收到的權杖內容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
在 [管理] 底下選取 [使用者流程]。

按一下刀鋒視窗頂端的 [+新增使用者流程]。

在 [選取使用者流程類型] 底下，選取 [全部]，然後選取要使用的 [登入] 版本。

[名稱]  決定您的應用程式所使用的登入使用者流程名稱。 例如，輸入 **SiIn**。

選取 [識別提供者] 底下的選項。 您也可以選取社交身分識別提供者 (如果已經設定)。 按一下 [確定]。

在 [應用程式宣告] 底下，按一下 [顯示更多]。

在 [傳回宣告] 欄中，選擇成功登入後，您要在權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱]、[識別提供者]、[郵遞區號] 和 [使用者的物件識別碼]。 按一下 [確定]。

按一下頁面底部的 [新增] 。 請注意，剛建立的使用者流程會顯示為 **B2C_1_SiIn** (自動新增 **B2C\_1\_** 部分)。

按一下 [執行使用者流程]。

在 [應用程式] 下拉式清單中選取 [Contoso B2C 應用程式]，在 [回覆 URL] 下拉式清單中選取 `https://localhost:44321/`。

按一下 [執行使用者流程]。 新的瀏覽器索引標籤隨即開啟，您可以開始進行取用者體驗來登入您的應用程式。

> [!NOTE]
> 建立和更新使用者流程後，需要經過一分鐘才會生效。
>

