---
title: 在 PIM-Azure Active Directory 中建立 Azure AD 角色的存取權檢閱 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中建立 Azure AD 角色的存取權檢閱。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0680ddf2c9e654455933bf09699ab81e8ab65d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65141835"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>在 PIM 中建立 Azure AD 角色的存取權檢閱

若要存取經過一段時間，Azure AD 角色的員工變更特殊權限。 若要減少過期的角色指派相關聯的風險，您應該定期檢閱存取權。 您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 來建立 Azure AD 角色特殊權限的存取權檢閱。 您也可以設定週期性的存取權檢閱自動發生。

本文說明如何建立一或多個存取權檢閱，Azure AD 角色特殊權限。

## <a name="prerequisites"></a>必要條件

- [特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>開啟存取權檢閱

1. 登入[Azure 入口網站](https://portal.azure.com/)與使用者特殊權限角色管理員角色的成員。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 在左側功能表中，按一下**Azure AD 角色**，然後按一下**存取權檢閱**。

1. 管理 下按一下 **存取權檢閱**。

    ![Azure AD 角色的存取權檢閱](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>開始存取權檢閱

在指定存取權檢閱的設定後，請按一下 [啟動]  。 存取權檢閱會出現在您的清單，其中包含其狀態的指標。

![存取權檢閱清單](./media/pim-how-to-start-security-review/access-reviews-list.png)

依預設，Azure AD 會在檢閱開始不久後傳送電子郵件給檢閱者。 如果您選擇不讓 Azure AD 傳送電子郵件，請務必通知檢閱者有存取權檢閱等待他們完成。 您可以提供他們如何指示[檢閱存取權給 Azure AD 角色](pim-how-to-perform-security-review.md)。

## <a name="manage-the-access-review"></a>管理存取權檢閱

當檢閱者完成其檢閱，您可以追蹤進度**概觀**的存取權檢閱 頁面。 在之前的目錄中變更任何存取權限[完成檢閱](pim-how-to-complete-review.md)。

![存取權檢閱進度](./media/pim-how-to-start-security-review/access-review-overview.png)

如果這是一次性的檢閱，然後存取權檢閱期間結束後或系統管理員停止存取權檢閱之後, 請依照下列中的步驟[完成 Azure AD 角色的存取權檢閱](pim-how-to-complete-review.md)來查看並套用結果。  

若要管理一系列的存取權檢閱，瀏覽至 [存取權檢閱，和您將即將推出的項目中尋找已排程檢閱和編輯的結束日期] 或新增/移除檢閱者據以。

根據您的選取項目，在**當設定完成時**，將會自動套用檢閱的結束日期，或當您以手動方式停止檢閱之後執行。 檢閱的狀態將會變更**已完成**歷經中繼狀態，例如**套用**最後狀態**套用**。 您應該會看到拒絕的使用者，如果有的話，在幾分鐘內從角色移除。

## <a name="next-steps"></a>後續步驟

- [檢閱 Azure AD 角色的存取權](pim-how-to-perform-security-review.md)
- [完成 Azure AD 角色的存取權檢閱](pim-how-to-complete-review.md)
- [建立 Azure 資源角色的存取權檢閱](pim-resource-roles-start-access-review.md)
