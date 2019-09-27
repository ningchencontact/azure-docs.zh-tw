---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326288"
---
1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選取包含您 Azure AD B2C 租使用者的目錄。
1. 在左側功能表中，選取 [ **Azure AD B2C**]。 或者，選取 [**所有服務**]，然後搜尋並選取 [ **Azure AD B2C**]。
1. 選取 [應用程式]，然後選取 [新增]。
1. 輸入應用程式的名稱。 例如，*nativeapp1*。
1. 若是**Native client**，請選取 **[是]** 。
1. 輸入具有唯一配置的**自訂重新導向 URI** 。 例如： `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。 選擇重新導向 URI 時，有兩個重要考量︰
    1. **唯一**：重新導向 URI 的配置對於每個應用程式都必須是唯一的。 在範例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`中，`com.onmicrosoft.contosob2c.exampleapp` 為配置。 應該遵循這個模式。 如果兩個應用程式共用相同配置，系統會讓使用者選擇應用程式。 如果使用者選擇不正確的，登入就會失敗。
    1. **完整**︰重新導向 URI 必須有配置和路徑。 路徑的網域後面必須至少包含一個正斜線。 例如，`//oauth/` 會在 `//oauth` 失敗時運作。 請勿在 URI 中包含特殊字元，例如底線。
1. 選取 [建立]。
