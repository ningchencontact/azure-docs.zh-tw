---
title: 教學課程-建立 Azure Active Directory B2C 租用戶
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
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056312"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教學課程：建立 Active Directory B2C 租用戶

應用程式必須先在您管理的租用戶中完成註冊，才可以與 Azure Active Directory (Azure AD) B2C 互動。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶

您會在下一個教學課程中了解如何註冊應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 請確定您使用包含您訂用帳戶的目錄。 按一下 **目錄和訂用帳戶篩選**在上方功能表中，然後選取包含您訂用帳戶的目錄。 這個目錄是不同於另一個將包含您的 Azure AD B2C 租用戶。

    ![切換至訂用帳戶目錄](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. 選擇 Azure 入口網站左上角的 [建立資源]  。
4. 搜尋並選取 [Active Directory B2C]  ，然後按一下 [建立]  。
5. 選擇**建立新的 Azure AD B2C 租用戶**輸入組織名稱和初始網域名稱。 選取國家/地區 （它之後無法變更），然後按一下**建立**。

    初始網域名稱做為您的租用戶名稱的一部分。 在此範例中，租用戶名稱為*contoso0926Tenant.onmicrosoft.com*:

    ![建立租用戶](./media/tutorial-create-tenant/create-tenant.PNG)

6. 在 **建立新的 B2C 租用戶或連結至現有的租用戶**頁面上，選擇**我的 Azure 訂用帳戶的連結現有的 Azure AD B2C 租用戶**。

    選取您所建立的租用戶，然後選取您的訂用帳戶。

    針對 資源群組中，選取**新建**。 輸入將包含租用戶的資源群組名稱，選取該位置，然後按一下 [建立]  。
1. 若要開始使用新的租用戶，請確定您使用按一下包含您的 Azure AD B2C 租用戶的目錄**目錄和訂用帳戶篩選**頂端的功能表，然後選擇包含它的目錄中。

    如果一開始您沒有看到新的 Azure B2C 租用戶清單中，重新整理瀏覽器視窗，然後選取**目錄和訂用帳戶篩選**上方功能表中，一次。

    ![切換至租用戶目錄](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶

接下來，了解如何在新的租用戶中註冊 web 應用程式。

> [!div class="nextstepaction"]
> [註冊您的應用程式 >](tutorial-register-applications.md)
