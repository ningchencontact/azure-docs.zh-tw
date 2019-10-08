---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720266"
---
Web API 資源必須先在您的租用戶中註冊，才能接受及回應受到用戶端應用程式保護而提供存取權杖的資源要求。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]  篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]  。 或者，選取 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
1. 選取 [應用程式]  ，然後選取 [新增]  。
1. 輸入應用程式的名稱。 例如，*webapi1*。
1. 針對 [Web 應用程式/Web API]  ，選取 [是]  。
1. 針對 [允許隱含流程]  ，選取 [是]  。
1. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 在本教學課程中，範例會在本機執行並在 `https://localhost:5000` 接聽。
1. 針對 [應用程式識別碼 URI]  ，將 API 端點識別碼新增至顯示的 URI。 在本教學課程中請輸入 `api`，讓完整的 URI 類似於 `https://contosob2c.onmicrosoft.com/api`。
1. 選取 [建立]  。
1. 記錄 [應用程式識別碼]  ，以便在稍後的步驟中使用。
