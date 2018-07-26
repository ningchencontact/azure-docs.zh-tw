---
title: 教學課程 - 建立 Azure Active Directory B2C 租用戶 | Microsoft Docs
description: 了解如何使用 Azure 入口網站建立 Azure Active Directory B2C 租用戶，以準備註冊應用程式。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc48cd3eb40d93c26a68caf843a89f7bbfb46c6c
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236888"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教學課程：建立 Azure Active Directory B2C 租用戶

應用程式必須先在您管理的租用戶中完成註冊，才可以與 Azure Active Directory (Azure AD) B2C 互動。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶

您會在下一個教學課程中了解如何註冊應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶

1. 選擇 Azure 入口網站左上角的 [建立資源]。
2. 在 Azure Marketplace 資源清單上方的搜尋方塊中，搜尋並選取 **Active Directory B2C**，然後按一下 [建立]。
3. 選擇 [建立新的 Azure AD B2C 租用戶]、輸入組織名稱和初始網域名稱 (用於租用戶名稱中)、選取國家/地區，然後按一下 [建立]。 請確定租用戶的國家/地區，此項目之後就無法變更。

    ![建立租用戶](./media/tutorial-create-tenant/create-tenant.png)

    在此範例中，租用戶名稱是 contoso0522Tenant.onmicrosoft.com

若要開始管理新租用戶，請對指出**按一下這裡以管理新的目錄**中的**這裡**兩字按一下。 您會看到**疑難排解**訊息，內容指出您必須將訂用帳戶連結至新的租用戶。 

## <a name="link-your-tenant-to-your-subscription"></a>將您的租用戶連結至您的訂用帳戶

您必須將您的 Azure AD B2C 租用戶連結至您的 Azure 訂用帳戶，才能啟用所有功能，並支付使用費用。 如果不將租用戶連結至訂用帳戶，應用程式將不會正確運作。

1. 切換 Azure 入口網站右上角的目錄，以確定您所使用的目錄包含要與新租用戶相關聯的訂用帳戶。

    ![切換目錄](./media/tutorial-create-tenant/switch-directories.png)

    然後選取包含訂用帳戶的目錄。

    ![選取目錄](./media/tutorial-create-tenant/select-directory.png)

2. 選擇 Azure 入口網站左上角的 [建立資源]。
3. 在 Azure Marketplace 資源清單上方的搜尋方塊中，搜尋並選取 **Active Directory B2C**，然後按一下 [建立]。
4. 選擇 [將現有 Azure AD B2C 租用戶連結至 Azure 訂用帳戶]、選取您所建立的租用戶、選取訂用帳戶、在資源群組名稱中輸入「myContosoTenantRG」、接受位置，然後按一下 [建立]。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶

> [!div class="nextstepaction"]
> [讓 Web 應用程式使用帳戶進行驗證](active-directory-b2c-tutorials-web-app.md)