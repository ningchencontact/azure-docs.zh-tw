---
title: 建立和管理 Azure AD 權限管理 （預覽）-Azure Active Directory 中的類別目錄
description: 了解如何在 Azure Active Directory 權限管理 （預覽） 中建立資源和存取套件的新容器。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541611"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>建立和管理 Azure AD 權限管理 （預覽） 中的類別目錄

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-a-catalog"></a>建立目錄

目錄是資源和存取封裝的容器。 當您想要相關的資源群組，並存取封裝時，您會建立目錄。 人會建立目錄，就會變成第一個類別目錄擁有者。 目錄擁有者可以新增額外的類別目錄的擁有者。

**必要的角色：** 使用者系統管理員或目錄的建立者

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 按一下  **Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**目錄**。

    ![在 Azure 入口網站中的權限管理目錄](./media/entitlement-management-catalog-create/catalogs.png)

1. 按一下 **新的類別目錄**。

1. 輸入類別目錄的唯一名稱，並提供描述。

    使用者會看到這項資訊存取封裝的詳細資料。

1. 如果您想要存取套件，可供使用者要求，一旦他們建立此目錄中的，設定**Enabled**要**是**。

1. 如果您想要允許使用者在選取的外部目錄，才能要求存取此目錄中的套件，請設定**外部使用者啟用**要**是**。

    ![新的 [目錄] 窗格](./media/entitlement-management-catalog-create/new-catalog.png)

1. 按一下 **建立**來建立目錄。

## <a name="add-resources-to-a-catalog"></a>將資源新增至目錄

若要存取封裝中包含的資源，資源必須存在於目錄中。 您可以新增的資源類型是群組、 應用程式和 SharePoint Online 網站。

**必要的角色：** 使用者管理員或目錄的擁有者

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**目錄**，然後開啟您想要將資源新增到的目錄。

1. 在左側功能表中，按一下**資源**。

1. 按一下 **將資源新增**。

1. 按一下 資源類型：**群組**，**應用程式**，或**SharePoint 網站**。

    如果您是目錄的建立者，您可以新增任何 Office 365 群組或您所擁有您的類別目錄的 Azure AD 安全性群組。 如果您想要指派給使用者的群組，但不是您擁有的群組，您必須已將該群組新增至您的類別目錄的使用者系統管理員。

    如果您是目錄的建立者，您可以新增您所擁有，包括 SaaS 應用程式與您的應用程式類別目錄的 Azure ad 同盟的任何 Azure AD 企業應用程式。 如果您想要指派使用者，但不是擁有的應用程式，您必須已加入該類別目錄的應用程式使用者管理員。 一旦應用程式類別目錄的一部分，您可以存取封裝中選取的任何應用程式的角色。

1. 選取您想要新增至類別目錄類型的一或多個資源。

1. 完成後，按一下**新增**。

    現在可以在目錄中存取套件中包含這些資源。

## <a name="remove-resources-from-a-catalog"></a>從目錄移除資源

您可以從目錄移除資源。 資源只有不在任何類別目錄的存取封裝中使用的情況下，只可以從目錄中移除。

**必要的角色：** 使用者管理員或目錄的擁有者

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**目錄**，然後開啟您想要移除的資源的目錄。

1. 在左側功能表中，按一下**資源**。

1. 選取您想要移除的資源。

1. 按一下 **移除**(或按一下省略符號 (**...**)，然後按一下 **移除資源**)。

## <a name="add-catalog-owners-or-access-package-managers"></a>新增目錄擁有者或存取套件管理員

如果您想要委派管理目錄或目錄中的存取套件，您會新增類別目錄的擁有者，或存取套件管理員。 人會建立目錄，就會變成第一個類別目錄擁有者。

**必要的角色：** 使用者管理員或目錄的擁有者

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**目錄**，然後開啟您想要新增系統管理員的目錄。

1. 在左側功能表中，按一下**角色和系統管理員**。

1. 按一下 **新增擁有者**或是**加入存取封裝管理員**選取這些角色的成員。

1. 按一下 **選取**以便將這些成員。

## <a name="edit-a-catalog"></a>編輯類別目錄

您可以編輯的名稱和目錄的描述。 使用者會看到這項資訊存取封裝的詳細資料。

**必要的角色：** 使用者管理員或目錄的擁有者

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**目錄**，然後開啟您想要編輯的目錄。

1. 在目錄上**概觀**頁面上，按一下**編輯**。

1. 編輯類別目錄的名稱或描述。

1. 按一下 [檔案] 。

## <a name="delete-a-catalog"></a>刪除類別目錄

如果它沒有任何存取套件，您可以刪除的目錄，但僅限於。

**必要的角色：** 使用者管理員或目錄的擁有者

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**目錄**，然後開啟您想要刪除的目錄。

1. 在目錄上**概觀**，按一下**刪除**。

1. 在出現的訊息方塊中，按一下 [確定]。

## <a name="next-steps"></a>後續步驟

- [建立和管理存取套件](entitlement-management-access-package-create.md)
