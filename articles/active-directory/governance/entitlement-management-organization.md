---
title: 在 Azure AD 權利管理（預覽）中新增已連線的組織-Azure Active Directory
description: 瞭解如何允許組織外部人員要求存取套件，讓您可以在專案上共同作業。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d45db1f62b370a2692ed932572e16c247b8903
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952481"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理（預覽）中新增已連線的組織

Azure AD 權利管理可讓您與組織外部的人員共同作業。 如果您經常與外部 Azure AD 目錄或網域中的使用者共同作業，您可以將他們新增為已連線的組織。 本文說明如何新增已連線的組織，讓您的組織外部使用者可以要求您的目錄中的資源。

## <a name="what-is-a-connected-organization"></a>什麼是已連線的組織？

已連線的組織是與您有關聯性的外部 Azure AD 目錄或網域。

例如，假設您在 Woodgrove Bank 工作，而且想要與兩個外部組織共同作業：圖形設計研究所和 Contoso。 您已由您的連絡人在使用 Azure AD 的圖形設計機構中告訴您，而且該圖形設計人員的使用者具有以 `graphicdesigninstitute.com`結尾的使用者主體名稱。 而且您是在 Contoso 的連絡人告訴您，他們尚未使用 Azure AD，但 Contoso 的使用者擁有以 `contoso.com`結尾的使用者主體名稱。

您可以設定兩個已連線的組織--一個用於具有網域 `graphicdesigninstitute.com`的圖形設計研究所，另一個用於具有網域 `contoso.com`的 Contoso。 如果您接著將這兩個已連線的組織新增至原則，則每個組織中具有符合原則之使用者主體名稱的使用者，都可以要求存取封裝。 此外，由於圖形設計局已識別為使用 Azure AD，因此，如果圖形設計局後面有子域（例如 `graphicdesigninstitute.example`），則具有該使用者主體名稱的使用者也可以使用來要求存取套件相同的原則。

![已連線的組織範例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 目錄或網域中的使用者將如何驗證，取決於驗證類型。 已連線組織的驗證類型如下：

| 驗證類型 | 狀態 |
| --- | --- |
| Azure AD | 支援的 |
| [直接同盟](../b2b/direct-federation.md) | 預覽 |
| [一次性密碼](../b2b/one-time-passcode.md)（網域） | 預覽 |

## <a name="add-a-connected-organization"></a>新增已連線的組織

請遵循下列步驟，將外部 Azure AD 目錄或網域新增為已連線的組織。

**先決條件角色：** 全域管理員、使用者系統管理員或來賓邀請者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**已連接的組織**]，然後按一下 [**新增已連線的組織**]。

    ![身分識別治理-已連線的組織-新增已連線的組織](./media/entitlement-management-organization/connected-organization.png)

1. 在 [**基本**] 索引標籤上，輸入組織的 [顯示名稱] 和 [描述]。

    ![[新增已連線的組織-基本] 索引標籤](./media/entitlement-management-organization/organization-basics.png)

1. 在 [**目錄 + 網域**] 索引標籤上，按一下 [**新增目錄 + 網域**] 以開啟 [選取目錄 + 網域] 窗格。

1. 輸入功能變數名稱，以搜尋 Azure AD 目錄或網域。 您必須輸入完整的功能變數名稱。

1. 依照提供的名稱和驗證類型，確認它是正確的組織。 使用者登入的方式將取決於驗證類型。

    ![新增已連線的組織-選取目錄 + 網域](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. 按一下 [**新增**] 以新增 Azure AD 目錄或網域。 目前，您只能為每個連線的組織新增一個 Azure AD 目錄或網域。

    > [!NOTE]
    > Azure AD 目錄或網域中的所有使用者都可以要求此存取封裝。 這包括從與目錄相關聯的所有子域 Azure AD 的使用者，除非 Azure B2B 允許或拒絕清單封鎖這些網域。 如需詳細資訊，請參閱[允許或封鎖對特定組織的 B2B 使用者的邀請](../b2b/allow-deny-list.md)。

1. 新增 Azure AD 目錄或網域之後，請按一下 [**選取**]。

    組織會出現在清單中。

    ![[新增已連線的組織-目錄] 索引標籤](./media/entitlement-management-organization/organization-directory-domain.png)

1. 在 [**贊助**者] 索引標籤上，為此連線組織新增選用的贊助者。

    贊助者是已在您目錄中的內部或外部使用者，這是與此連線組織的關係點。 內部贊助者是您目錄中的成員使用者。 外部贊助者是來自已連線組織的來賓使用者，先前已受邀並已在您的目錄中。 當此連線組織中的使用者要求存取此存取套件時，可將贊助者當做核准者使用。 如需如何將來賓使用者邀請至您目錄的詳細資訊，請參閱[在 Azure 入口網站中新增 AZURE ACTIVE DIRECTORY B2B](../b2b/add-users-administrator.md)共同作業使用者。

    當您按一下 [**新增/移除**] 時，就會出現一個窗格，供您選取內部或外部贊助者。 此窗格會在您的目錄中顯示未篩選的使用者和群組清單。

    ![存取套件-原則-新增已連線的組織-贊助商索引標籤](./media/entitlement-management-organization/organization-sponsors.png)

1. 在 [**審查 + 建立**] 索引標籤上，檢查您的組織設定，然後按一下 [**建立**]。

    ![存取套件-原則-新增已連線的組織-審核 + 建立索引標籤](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>刪除已連線的組織

如果您不再具有外部 Azure AD 目錄或網域的關聯性，您可以刪除已連線的組織。

**先決條件角色：** 全域管理員、使用者系統管理員或來賓邀請者

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中，按一下 [**已連接的組織**]，然後按一下以開啟 [已連線的組織]。

1. 在 [總覽] 頁面上，按一下 [**刪除**] 以刪除已連線的組織。

    目前，只有在沒有已連線的使用者時，您才能刪除已連線的組織。

    ![身分識別治理-已連線的組織-刪除已連線的組織](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>後續步驟

- [管理外部使用者的存取權](entitlement-management-organization.md)
- [針對不在您目錄中的使用者](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
