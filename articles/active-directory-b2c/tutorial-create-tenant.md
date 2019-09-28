---
title: 教學課程-建立 Azure Active Directory B2C 租使用者
description: 了解如何使用 Azure 入口網站建立 Azure Active Directory B2C 租用戶，以準備註冊應用程式。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345220"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教學課程：建立 Active Directory B2C 租用戶

您的應用程式必須先在您管理的租使用者中註冊，才可以與 Azure Active Directory B2C （Azure AD B2C）互動。

在本文中，您將了解：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶
> * 切換至包含您 Azure AD B2C 租使用者的目錄
> * 在 Azure 入口網站中將 Azure AD B2C 資源新增為我的*最愛*

您會在下一個教學課程中了解如何註冊應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 請確定您使用的是包含訂用帳戶的目錄。

    在頂端功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選取包含您訂用帳戶的目錄。 此目錄不同于將包含您 Azure AD B2C 租使用者的目錄。

    ![已選取訂用帳戶租使用者的目錄 + 訂用帳戶篩選](media/tutorial-create-tenant/portal-01-select-directory.png)

1. 在 Azure 入口網站的左上角，選取 [**建立資源**]。
1. 搜尋並選取 [ **Active Directory B2C**]，然後選取 [**建立**]。
1. 選取 [建立新的 Azure AD B2C 租用戶]。

    ![建立在 Azure 入口網站中選取的新 Azure AD B2C 租使用者](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. 輸入 [**組織名稱**] 和 [**初始功能變數名稱**]。 選取 [**國家（地區**）] 或 [區域] （之後無法變更），然後選取 [**建立**]。

    功能變數名稱是用來作為完整租使用者功能變數名稱的一部分。 在此範例中，租使用者名稱是*contosob2c.onmicrosoft.com*：

    ![使用 Azure 入口網站中的範例值，在中建立租使用者表單](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. 租使用者建立完成後，請選取 [租使用者建立] 頁面頂端的 [**建立新的 B2C 租使用者] 或 [連結至現有的租**使用者] 連結。

    ![已反白顯示 Azure 入口網站中的連結租使用者階層連結連結](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. 選取 [將**現有的 Azure AD B2C 租使用者連結至我的 Azure 訂**用帳戶]。

   ![在 Azure 入口網站中連結現有的訂用帳戶選取專案](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. 選取您建立的**Azure AD B2C 租**使用者，然後選取您的**訂**用帳戶。

    在 [資源群組] 中，選取 [新建]。 輸入將包含租使用者的資源組**名**，選取**資源群組位置**，然後選取 [**建立**]。

    ![連結 Azure 入口網站中的訂用帳戶設定表單](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>選取您的 B2C 租使用者目錄

若要開始使用新的 Azure AD B2C 租使用者，您必須切換至包含租使用者的目錄。

在 Azure 入口網站的上方功能表中選取 [**目錄 + 訂**用帳戶] 篩選，然後選取包含您 Azure AD B2C 租使用者的目錄。

如果您先在清單中看不到新的 Azure B2C 租使用者，請重新整理瀏覽器視窗，然後在頂端功能表中再次選取 [**目錄 + 訂**用帳戶] 篩選。

![B2C 租使用者-包含在 Azure 入口網站中選取的目錄](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>將 Azure AD B2C 新增為我的最愛（選擇性）

這個選擇性步驟可讓您更輕鬆地在下列和所有後續教學課程中選取您的 Azure AD B2C 租使用者。

您不需要在每次想要使用租使用者時搜尋**所有服務**中的「Azure AD B2C」，而可以改為將資源加入最愛。 然後，您可以從左側的 [我的最愛 **] 功能表中**選取它，以快速流覽至您的 Azure AD B2C 租使用者。

您只需要執行此作業一次。 執行這些步驟之前，請確定您已切換至包含 Azure AD B2C 租使用者的目錄，如上一節[選取 B2C 租使用者目錄](#select-your-b2c-tenant-directory)中所述。

1. 選取左側功能表中的 [**所有服務**] [Azure 入口網站](https://portal.azure.com)
1. 在 [搜尋] 文字方塊中輸入*Azure AD B2C*
1. 選取**星號**以將 Azure AD B2C 新增至我的最愛
1. *Azure AD B2C*現在會顯示在 [我的最愛 **]** 左側功能表中。 您可以視需要在清單中選取並將它拖曳至較高的位置，如下圖所示：

![在 Azure 入口網站中將 Azure AD B2C 新增為我的最愛的步驟](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 建立 Azure AD B2C 租用戶
> * 將您的租用戶連結至您的訂用帳戶
> * 切換至包含您 Azure AD B2C 租使用者的目錄
> * 在 Azure 入口網站中將 Azure AD B2C 資源新增為我的*最愛*

接下來, 瞭解如何在新的租使用者中註冊 web 應用程式。

> [!div class="nextstepaction"]
> [註冊您的應用程式 >](tutorial-register-applications.md)
