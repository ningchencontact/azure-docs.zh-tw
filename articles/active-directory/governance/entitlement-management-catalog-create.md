---
title: 在 Azure AD 權利管理 (預覽) 中建立及管理目錄-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理 (預覽) 中建立資源的新容器和存取封裝。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1654e0a8cd11ac1c7a2f4ef0667d0e99187c2374
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618323"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理 (預覽) 中建立及管理目錄

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-a-catalog"></a>建立目錄

目錄是資源和存取封裝的容器。 當您想要將相關的資源和存取封裝分組時, 請建立類別目錄。 建立類別目錄的人員會成為第一個目錄擁有者。 目錄擁有者可以新增其他目錄擁有者。

**必要角色：** 全域管理員、使用者系統管理員或目錄建立者

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**目錄**]。

    ![Azure 入口網站中的權利管理目錄](./media/entitlement-management-catalog-create/catalogs.png)

1. 按一下 [**新增目錄**]。

1. 輸入目錄的唯一名稱, 並提供描述。

    使用者會在存取套件的詳細資料中看到這項資訊。

1. 如果您想要讓使用者在建立此目錄中的存取套件時可以立即要求, 請將 [**啟用**] 設定為 **[是]** 。

1. 如果您想要允許選取的外部目錄中的使用者能夠要求此目錄中的存取封裝, 請將 [**為外部使用者啟用**] 設為 **[是]** 。

    ![新增目錄窗格](./media/entitlement-management-catalog-create/new-catalog.png)

1. 按一下 [**建立**] 以建立類別目錄。

## <a name="add-resources-to-a-catalog"></a>將資源新增至目錄

若要在存取封裝中包含資源, 資源必須存在於目錄中。 您可以新增的資源類型包括 [群組]、[應用程式] 和 [SharePoint Online 網站]。 這些群組可以是雲端建立的 Office 365 群組或雲端建立的 Azure AD 安全性群組。 應用程式可以 Azure AD 企業應用程式, 包括 SaaS 應用程式和您自己的應用程式 (與 Azure AD 聯盟)。 網站可以是 SharePoint Online 網站或 SharePoint Online 網站集合。

**必要角色：** 請參閱[將資源新增至目錄的必要角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**目錄**], 然後開啟您想要新增資源的目錄。

1. 在左側功能表中, 按一下 [**資源**]。

1. 按一下 [**新增資源**]。

1. 按一下 [資源類型]:**群組**、**應用程式**或**SharePoint 網站**。

    如果您沒有看到想要新增的資源, 或無法新增資源, 請確定您擁有必要的 Azure AD directory 角色和權利管理角色。 您可能需要具有必要角色的人員, 才能將資源新增至您的目錄。 如需詳細資訊, 請參閱[將資源新增至目錄的必要角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

1. 選取您要新增至目錄之類型的一或多個資源。

1. 完成後, 按一下 [**新增**]。

    這些資源現在可以包含在目錄內的存取封裝中。

## <a name="remove-resources-from-a-catalog"></a>從目錄中移除資源

您可以從目錄中移除資源。 只有當資源未在目錄的任何存取套件中使用時, 才可以從目錄中移除它。

**必要角色：** 請參閱[將資源新增至目錄的必要角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**目錄**], 然後開啟您想要從中移除資源的目錄。

1. 在左側功能表中, 按一下 [**資源**]。

1. 選取您想要移除的資源。

1. 按一下 [**移除**] (或按一下省略號 ( **...** ), 然後按一下 [**移除資源**])。

## <a name="edit-a-catalog"></a>編輯目錄

您可以編輯目錄的名稱和描述。 使用者會在存取套件的詳細資料中看到這項資訊。

**必要角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**目錄**], 然後開啟您想要編輯的目錄。

1. 在目錄的 **[總覽**] 頁面上, 按一下 [**編輯**]。

1. 編輯目錄的 [名稱] 或 [描述]。

1. 按一下 [儲存]。

## <a name="delete-a-catalog"></a>刪除目錄

您可以刪除類別目錄, 但只有在它沒有任何存取套件時才可刪除。

**必要角色：** 全域管理員、使用者系統管理員或目錄擁有者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**目錄**], 然後開啟您想要刪除的目錄。

1. 在目錄的**總覽**中, 按一下 [**刪除**]。

1. 在出現的訊息方塊中，按一下 [確定]。

## <a name="next-steps"></a>後續步驟

- [新增目錄建立者](entitlement-management-delegate.md#add-a-catalog-creator)
- [建立和管理存取套件](entitlement-management-access-package-create.md)
