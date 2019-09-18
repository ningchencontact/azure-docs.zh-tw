---
title: 教學課程-註冊應用程式-Azure Active Directory B2C
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中註冊 Web 應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 06b2c273f41bfa74ee968b6fd6676e83767ce8b2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063260"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中註冊應用程式

您的[應用程式](active-directory-b2c-apps.md)必須先在您管理的租使用者中註冊，才可以與 Azure Active Directory B2C （Azure AD B2C）互動。 本教學課程示範如何使用 Azure 入口網站註冊 Web 應用程式。

在本文中，您將了解：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

如果您尚未建立自己的 [Azure AD B2C 租用戶](tutorial-create-tenant.md)，請立即建立一個。 您可以使用現有的 Azure AD B2C 租用戶。

## <a name="register-a-web-application"></a>註冊 Web 應用程式

1. 請選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含您租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式]，然後選取 [新增]。
1. 輸入應用程式的名稱。 例如，*webapp1*。
1. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
1. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 例如，您可以將它設定為在`https://localhost:44316`本機接聽。 如果還不知道連接埠號碼，您可以輸入預留位置值，之後再變更。

    基於此教學課程的測試目的，您可以將`https://jwt.ms`它設定為，以顯示要檢查之權杖的內容。 在本教學課程中，請將 [ `https://jwt.ms`**回復 URL** ] 設定為。

    下列限制適用于回復 Url：

    * 回復 URL 的開頭必須是配置`https`。
    * 回復 URL 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑`.../abc/response-oidc`的一部分，請勿在 [回復 URL] 中指定。 `.../ABC/response-oidc` 由於網頁瀏覽器會將路徑視為區分大小寫，因此如果`.../abc/response-oidc`將與相關聯的 cookie 重新導向至不`.../ABC/response-oidc`相符的 URL，可能會予以排除。

1. 按一下 [**建立**] 以完成應用程式註冊。

## <a name="create-a-client-secret"></a>建立用戶端密碼

如果您的應用程式會交換權杖的程式碼，您必須建立應用程式密碼。

1. 在 [ **Azure AD B2C 應用**程式] 頁面中，選取您建立的應用程式，例如*webapp1*。
1. 選取 [**金鑰**]，然後選取 [**產生金鑰**]。
1. 選取 [儲存] 以檢視金鑰。 請記下 [應用程式金鑰] 值。 您會使用此值做為應用程式代碼中的應用程式密碼。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

接下來，瞭解如何建立使用者流程，讓您的使用者註冊、登入及管理其設定檔。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C > 中建立使用者流程](tutorial-create-user-flows.md)
