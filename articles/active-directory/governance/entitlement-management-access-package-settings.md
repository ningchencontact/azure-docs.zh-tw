---
title: 在 Azure AD 權利管理（預覽）中要求存取套件的共用連結-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理（預覽）中共用連結以要求存取套件。
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
ms.openlocfilehash: 91b98df1f93991d4781283bc38b02dc20d11268d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392328"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理（預覽）中要求存取套件的共用連結

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您目錄中的大部分使用者都可以登入我的存取權入口網站，並自動查看他們可以要求的存取套件清單。 不過，對於還不在您目錄中的外部商業夥伴使用者，您將需要傳送一個連結，讓他們可以用來要求存取封裝。 

只要針對[外部使用者啟用](entitlement-management-catalog-create.md)存取套件的目錄，而且您有[外部使用者目錄的原則](entitlement-management-access-package-request-policy.md)，外部使用者就可以使用我的存取權入口網站連結來要求存取套件。

## <a name="share-link-to-request-an-access-package"></a>共用連結以要求存取套件

**先決條件角色：** 全域管理員、使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**存取套件**]，然後開啟存取封裝。

1. 在 [總覽] 頁面上，複製 [**我的存取權入口網站] 連結**。

    ![存取套件概觀 - 我的存取權入口網站的連結](./media/entitlement-management-shared/my-access-portal-link.png)

    將整個我的存取權入口網站連結傳送給內部商業夥伴時，請務必將它複製。 這可確保合作夥伴可以存取您目錄的入口網站來提出要求。 連結的開頭為 `myaccess`，包含目錄提示，並以存取套件識別碼結尾。

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. 傳送電子郵件給您的外部商業夥伴，或將其連結。 他們可以與使用者共用連結，以要求存取封裝。

## <a name="next-steps"></a>後續步驟

- [要求存取權套件](entitlement-management-request-access.md)
- [核准或拒絕存取要求](entitlement-management-request-approve.md)