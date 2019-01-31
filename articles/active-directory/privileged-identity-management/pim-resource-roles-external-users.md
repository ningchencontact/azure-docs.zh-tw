---
title: 在 PIM 中邀請外部使用者及指派 Azure 資源角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中邀請外部使用者及指派 Azure 資源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f3cfb792b9c9befcbc2ee869ef5b31903e5c10f9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188604"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>在 PIM 中邀請外部使用者及指派 Azure 資源角色

Azure Active Directory (Azure AD) 企業對企業 (B2B) 是 Azure AD 內的一組功能，可讓組織使用任何帳戶與外部使用者和廠商共同作業。 當您結合 B2B 與 Azure AD Privileged Identity Management (PIM) 時，您可以繼續將您的合規性和治理需求套用到外部使用者。 例如，您可以使用適用於 Azure 資源的 PIM 功能搭配外部使用者：

- 將存取權指派給特定 Azure 資源
- 啟用 Just In Time 存取
- 指定指派持續時間和結束日期
- 有效指派或啟用都需要 MFA
- 執行存取權檢閱
- 利用警示和稽核記錄

本文說明如何邀請外部使用者到您的目錄，以及使用 PIM 管理其對於 Azure 資源的存取權。

## <a name="when-would-you-invite-external-users"></a>您何時會邀請外部使用者？

以下是您可能邀請外部使用者到您的目錄時的幾個範例案例：

- 允許只有電子郵件帳戶的外部自營廠商存取專案的 Azure 資源。
- 允許大型組織中使用內部部署 Active Directory Federation Services 的外部夥伴存取您的費用應用程式。
- 允許不在貴組織 (例如 Microsoft 支援服務) 的支援工程師暫時存取您的 Azure 資源，針對問題進行疑難排解。

## <a name="how-does-external-collaboration-using-b2b-work"></a>使用 B2B 的外部共同作業如何運作？

當您使用 B2B 時，您可以邀請外部使用者到您的目錄。 外部使用者似乎在您的目錄中，但使用者沒有任何與其相關聯的認證。 每當必須驗證外部使用者時，他們必須在其主目錄 (而非您的目錄) 中進行驗證。 這表示如果外部使用者無法再存取其主目錄，他們會自動喪失您目錄的存取權。 例如，如果外部使用者離開其組織，您不必採取任何動作，他們就會自動喪失您在目錄中與其共用的任何資源的存取權。 如需 B2B 的詳細資訊，請參閱[什麼是 Azure Active Directory B2B 中的來賓使用者存取權？](../b2b/what-is-b2b.md)。

![B2B 和外部使用者](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>檢查外部共同作業設定

若要確保您可以邀請外部使用者到您的目錄，您應該檢查您的外部共同作業設定。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 按一下 [Azure Active Directory] > [使用者設定]。

1. 按一下 [管理外部共同作業設定]。

    ![外部共同作業設定](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 確定 [系統管理員與具備「來賓邀請者角色」的使用者可邀請來賓] 參數設定為 [是]。

## <a name="invite-an-external-user-and-assign-a-role"></a>邀請外部使用者並指派角色

使用 PIM，您可以邀請外部使用者，使其符合 Azure 資源角色的資格，就如同成員使用者一樣。

1. 以屬於[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)或[使用者帳戶管理員](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator)角色一員的使用者身分登入 [Azure 入口網站](https://portal.azure.com/)。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 使用 [資源篩選] 來篩選管理的資源清單。

1. 按一下您想要管理的資源，例如資源、資源群組、訂用帳戶或管理群組。

    您應該將範圍設定為僅限外部使用者所需的資訊。

1. 按一下 [管理] 下方的 [角色]，以查看 Azure 資源角色的清單。

    ![Azure 資源角色](./media/pim-resource-roles-external-users/resources-roles.png)

1. 按一下使用者需要的最小角色。

    ![選取的角色](./media/pim-resource-roles-external-users/selected-role.png)

1. 在角色頁面上，按一下 [新增成員] 以開啟 [新增指派] 窗格。

1. 按一下 [選取成員或群組]。

    ![選取成員或群組](./media/pim-resource-roles-external-users/select-member-group.png)

1. 若要邀請外部使用者，請按一下 [邀請]。

    ![邀請來賓](./media/pim-resource-roles-external-users/invite-guest.png)

1. 在您指定外部使用者之後，請按一下 [邀請]。

    應該將外部使用者新增為選取的成員。

1. 在 [選取成員或群組] 窗格中，按一下 [選取]。

1. 在 [成員資格設定] 窗格中，選取指派類型和持續時間。

    ![成員資格設定](./media/pim-resource-roles-external-users/membership-settings.png)

1. 若要完成指派，請按一下 [完成]，然後 [新增]。

    外部使用者角色指派會出現在您的角色清單中。

    ![外部使用者的角色指派](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>以外部使用者的身分啟用角色

身為外部使用者，您必須先接受對 Azure AD 目錄的邀請，而且也許啟用您的角色。

1. 開啟含有您目錄邀請的電子郵件。 電子郵件會如下所示。

    ![電子郵件邀請](./media/pim-resource-roles-external-users/email-invite.png)

1. 按一下電子郵件中的 [開始] 連結。

1. 檢閱權限之後，按一下 [接受]。

    ![檢閱權限](./media/pim-resource-roles-external-users/invite-accept.png)

1. 系統可能會要求您接受使用規定，並指定您是否想要保持登入狀態。

    Azure 入口網站隨即開啟。 如果您剛具備某個角色的資格，則沒有資源的存取權。

1. 若要啟用您的角色，請開啟含有啟用角色連結的電子郵件。 電子郵件會如下所示。

    ![電子郵件邀請](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 按一下 [啟用角色] 以在 PIM 中開啟您有資格的角色。

    ![我的角色 - 合格](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 按一下 [動作] 下方的 [啟用] 連結。

    根據角色設定，您必須指定一些資訊才能啟用該角色。

1. 您指定了角色的設定之後，按一下 [啟用] 以啟用角色。

    ![啟用角色](./media/pim-resource-roles-external-users/activate-role.png)

    除非需要系統管理員核准您的要求，否則您應該具有所指定資源的存取權。

## <a name="view-activity-for-an-external-user"></a>檢視外部使用者的活動

就如同成員使用者，您可以檢視稽核記錄來追蹤外部使用者在做什麼。

1. 身為管理員，開啟 PIM 並選取已共用的資源。

1. 按一下 [資源稽核] 來檢視該資源的活動。 下列範例顯示資源群組的活動範例。

    ![資源稽核](./media/pim-resource-roles-external-users/audit-resource.png)

1. 若要檢視外部使用者的活動，請按一下 [Azure Active Directory] > [使用者] > 外部使用者。

1. 按一下 [稽核記錄] 以查看目錄的稽核記錄。 如有需要，您可以搜尋篩選條件。

    ![目錄稽核](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>後續步驟

- [在 PIM 中指派 Azure AD 目錄角色](pim-how-to-add-role-to-user.md)
- [什麼是 Azure Active Directory B2B 中的來賓使用者存取權？](../b2b/what-is-b2b.md)
