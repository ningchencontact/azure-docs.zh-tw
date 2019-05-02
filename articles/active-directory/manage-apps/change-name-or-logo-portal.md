---
title: 變更名稱或標誌的 Azure Active Directory 中企業應用程式 |Microsoft Docs
description: 如何變更的名稱或 Azure Active Directory 中的自訂企業應用程式的標誌
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbebc528227ebb06bd66e31b802dd4fd618d6a99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718747"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>變更名稱或標誌的 Azure Active Directory 中企業應用程式

在 Azure Active Directory (Azure AD) 中，您可以輕鬆變更自訂企業應用程式的名稱或標誌。 您必須擁有適當的權限，才能進行這些變更，而且您必須自訂的應用程式的建立者。

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>如何變更企業應用程式的名稱或標誌？

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)目錄全域管理員身分的帳戶。 **Azure Active Directory 系統管理中心**頁面隨即出現。
2. 在左側窗格中，選取 [企業應用程式]。 企業應用程式清單隨即出現。
3. 選取應用程式。 應用程式的 [概觀] 頁面隨即出現。
4. 應用程式 [概觀] 窗格中，在底下**管理**標題之下，選取**屬性**。 **屬性**頁面隨即出現。
5. 如果您想要變更名稱，選取**名稱** 方塊中，輸入新名稱，然後按**Enter**。
6. 如果您想要變更的標誌，尋找**標誌**欄位，然後選取資料夾圖示旁**選取檔案** 方塊中，也就是下列應用程式的目前標誌映像。

   ![選取 [屬性] 命令](./media/change-name-or-logo-portal/change-logo.png)

   否則，如果您不變更標誌，請移至步驟 8 中。
7. 在檔案選擇器中，選取您想要的檔案做為新的標誌。 檔案的名稱會出現在目前的標誌影像下方的方塊。

   > [!NOTE]
   > Azure 會要求為 PNG 檔案，標誌影像，然後套用寬度、 高度和檔案大小限制。
8. 選取 [ **儲存**]。 如果您選擇新的標誌**標誌**欄位的映像變更，以反映新的標誌檔案。

## <a name="next-steps"></a>後續步驟

* [快速入門：Azure Active Directory 中檢視您的組織群組和成員](../fundamentals/active-directory-groups-view-azure-portal.md)
* [將使用者或群組指派給企業應用程式](assign-user-or-group-access-portal.md)
* [從企業應用程式中移除使用者或群組指派](remove-user-or-group-access-portal.md)
* [Disable user sign-ins for an enterprise app](disable-user-sign-in-portal.md)
