---
title: 教學課程-建立 Azure Active Directory B2C 租使用者
description: 了解如何使用 Azure 入口網站建立 Azure Active Directory B2C 租用戶，以準備註冊應用程式。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 57aed26fe2f2f03dca347836f21d1cad14a07642
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063339"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教學課程：建立 Active Directory B2C 租用戶

您的應用程式必須先在您管理的租使用者中註冊，才可以與 Azure Active Directory B2C （Azure AD B2C）互動。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶

您會在下一個教學課程中了解如何註冊應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 請確定您使用的是包含訂用帳戶的目錄。 在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選取包含您訂用帳戶的目錄。 此目錄不同于將包含您 Azure AD B2C 租使用者的目錄。

    ![已選取訂用帳戶租使用者的目錄和訂用帳戶篩選](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. 選擇 Azure 入口網站左上角的 [建立資源]。
4. 搜尋並選取 [Active Directory B2C]，然後按一下 [建立]。
5. 選擇 [**建立新的 Azure AD B2C 租**使用者], 然後輸入 [組織名稱] 和 [初始功能變數名稱]。 選取 [國家/地區] (之後無法變更), 然後按一下 [**建立**]。

    初始功能變數名稱會用來做為租使用者名稱的一部分。 在此範例中, 租使用者名稱是*contoso0926Tenant.onmicrosoft.com*:

    ![Azure 入口網站中的 B2C 租使用者建立頁面](./media/tutorial-create-tenant/create-tenant.PNG)

6. 在 [**建立新的 B2C 租使用者或連結至現有租**使用者] 頁面上, 選擇 [將**現有的 Azure AD B2C 租使用者連結至我的 Azure 訂**用帳戶]

    選取您建立的租使用者, 然後選取您的訂用帳戶。

    針對 [資源群組], 選取 [**新建**]。 輸入將包含租用戶的資源群組名稱，選取該位置，然後按一下 [建立]。
1. 若要開始使用新的租使用者，請確定您使用的是包含 Azure AD B2C 租使用者的目錄，方法是選取頂端功能表中的 [**目錄 + 訂**用帳戶] 篩選，然後選擇包含它的目錄。

    如果您先在清單中看不到新的 Azure B2C 租使用者，請重新整理瀏覽器視窗，然後在頂端功能表中再次選取 [**目錄 + 訂**用帳戶] 篩選。

    ![已選取 B2C 租使用者的目錄和訂用帳戶篩選](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶

接下來, 瞭解如何在新的租使用者中註冊 web 應用程式。

> [!div class="nextstepaction"]
> [註冊您的應用程式 >](tutorial-register-applications.md)
