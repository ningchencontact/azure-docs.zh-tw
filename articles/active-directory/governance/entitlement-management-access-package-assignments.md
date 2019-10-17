---
title: 在 Azure AD 權利管理（預覽）中查看、新增和移除存取套件的指派-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理（預覽）中查看、新增和移除存取套件的指派。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 694c8866a69d8488511be1670ba3d3013a4a3423
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392510"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理（預覽）中查看、新增和移除存取套件的指派

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在 Azure AD 權利管理 中，您可以看到誰已獲指派存取封裝、其原則和狀態。 如果存取套件具有適當的原則，您也可以直接將使用者指派給存取封裝。 本文說明如何查看、新增和移除存取封裝的指派。

## <a name="view-who-has-an-assignment"></a>查看誰具有指派

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 按一下 [**指派**]，以查看作用中的指派清單。

    ![存取套件的指派清單](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 按一下特定的指派以查看其他詳細資料。

1. 若要查看未正確布建所有資源角色的指派清單，請按一下 [篩選狀態]，然後選取 [**傳遞**]。

    您可以在 [**要求**] 頁面上尋找使用者的對應要求，以查看傳遞錯誤的其他詳細資料。

1. 若要查看過期的指派，請按一下篩選狀態，然後選取 [已**過期**]。

1. 若要下載已篩選清單的 CSV 檔案，請按一下 [**下載**]。

## <a name="directly-assign-a-user"></a>直接指派使用者

在某些情況下，您可能會想要將特定使用者直接指派給存取套件，讓使用者不必完成要求存取套件的程式。 若要直接指派使用者，存取套件必須具有允許系統管理員直接指派的原則。

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在左側功能表中，按一下 [**指派**]。

1. 按一下 [**新增指派**] 以開啟 [將使用者新增至存取套件]。

    ![指派-將使用者新增至存取封裝](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. 按一下 [**新增使用者**] 以選取您想要指派存取套件的使用者。

1. 在 [**選取原則**] 清單中，選取具有 [[無（僅限系統管理員直接指派）](entitlement-management-access-package-request-policy.md#none-administrator-direct-assignments-only) ] 設定的原則。

    如果此存取套件沒有這種類型的原則，您可以按一下 [**建立新原則**] 來加入一個。

1. 設定您想要選取的使用者指派開始和結束的日期和時間。 如果未提供結束日期，則會使用原則的生命週期設定。

1. 選擇性地為記錄保留的直接指派提供理由。

1. 按一下 [**新增**]，將選取的使用者直接指派給存取封裝。

    幾分鐘後 **，按一下 [** 重新整理] 以查看 [指派] 清單中的使用者。

## <a name="next-steps"></a>後續步驟

- [存取套件的變更要求和設定](entitlement-management-access-package-request-policy.md)
- [查看報表和記錄](entitlement-management-reports.md)
