---
title: 邀請來賓，並將 PIM-Azure Active Directory 中的 Azure 資源角色指派 |Microsoft Docs
description: 了解如何邀請外部來賓使用者，並將在 Azure AD Privileged Identity Management (PIM) 中的 Azure 資源角色指派。
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
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07476c9f5db64a5d107a493022fa3548fe0dae4c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476348"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>邀請來賓使用者，並將在 PIM 中的 Azure 資源角色指派

Azure Active Directory (Azure AD) 企業對企業 (B2B) 是一組功能，可讓組織共同作業與外部來賓使用者 （來賓） 和廠商使用的任何帳戶的 Azure AD 內。 當您結合 B2B 與 Azure AD Privileged Identity Management (PIM) 時，您可以繼續為遊客套用您的合規性和控管需求。 例如，您可以使用 Azure 身分識別工作的這些 PIM 功能，來賓：

- 將存取權指派給特定 Azure 資源
- 啟用 Just In Time 存取
- 指定指派持續時間和結束日期
- 有效指派或啟用都需要 MFA
- 執行存取權檢閱
- 利用警示和稽核記錄

本文說明如何邀請來賓至您的組織和管理其存取 Azure 資源使用 Privileged Identity Management。

## <a name="when-would-you-invite-guests"></a>何時您想邀請來賓？

您可以邀請來賓至您的組織時，以下是幾個範例案例：

- 允許只有電子郵件帳戶的外部自營廠商存取專案的 Azure 資源。
- 允許大型組織中使用內部部署 Active Directory Federation Services 的外部夥伴存取您的費用應用程式。
- 允許不在貴組織 (例如 Microsoft 支援服務) 的支援工程師暫時存取您的 Azure 資源，針對問題進行疑難排解。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>如何進行共同作業使用 B2B 來賓工作嗎？

當您使用 B2B 共同作業時，您可以邀請外部使用者到您的組織以來賓身分。 客體會顯示在您的組織，但客體並沒有任何與其相關聯的認證。 每當來賓必須進行驗證，他們必須通過驗證，在其所屬組織，而不是在您的組織。 這表示，如果來賓不再具有其主要組織的存取權，他們也無法存取您的組織。 比方說，如果客體離開組織，他們會自動無法存取您與他們共用 Azure AD 中，您不必採取任何動作的任何資源。 如需 B2B 的詳細資訊，請參閱[什麼是 Azure Active Directory B2B 中的來賓使用者存取權？](../b2b/what-is-b2b.md)。

![此圖的顯示如何來賓使用者會出現在您的目錄，但其主目錄驗證](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>請檢查客體共同作業設定

若要確定您可邀請來賓至您的組織，您應該檢查客體共同作業設定。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 按一下 [Azure Active Directory]   > [使用者設定]  。

1. 按一下 [管理外部共同作業設定]  。

    ![顯示權限、 邀請和共同作業限制設定的外部共同作業設定頁面](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. 確定 [系統管理員與具備「來賓邀請者角色」的使用者可邀請來賓]  參數設定為 [是]  。

## <a name="invite-a-guest-and-assign-a-role"></a>邀請來賓，並指派角色

使用 PIM，您就可以邀請來賓，並使其符合資格的 Azure 資源角色的成員使用者一樣。

1. 登入[Azure 入口網站](https://portal.azure.com/)隸屬的使用者[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)或是[使用者管理員](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)角色。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]  。

1. 使用 [資源篩選]  來篩選管理的資源清單。

1. 按一下您想要管理的資源，例如資源、資源群組、訂用帳戶或管理群組。

    您應該將範圍設定只需要哪些客體。

1. 按一下 [管理] 下方的 [角色]  ，以查看 Azure 資源角色的清單。

    ![Azure 資源角色 清單顯示為作用中和合格的使用者數目](./media/pim-resource-roles-external-users/resources-roles.png)

1. 按一下使用者需要的最小角色。

    ![選取 [角色] 頁面上列出該角色的目前成員](./media/pim-resource-roles-external-users/selected-role.png)

1. 在角色頁面上，按一下 [新增成員]  以開啟 [新增指派] 窗格。

1. 按一下 [選取成員或群組]  。

    ![新指派-選取的成員或群組 窗格，列出使用者和群組以及邀請選項](./media/pim-resource-roles-external-users/select-member-group.png)

1. 若要邀請來賓，請按一下**邀請**。

    ![邀請來賓頁面中的，輸入電子郵件地址，並指定個人訊息方塊](./media/pim-resource-roles-external-users/invite-guest.png)

1. 您已選取 來賓之後，請按一下**邀請**。

    客體應該將選取的成員。

1. 在 **選取的成員或群組**窗格中，按一下**選取**。

1. 在 [**成員資格設定**] 窗格中，選取指派類型和持續時間。

    ![新指派-成員資格設定 頁面來指定指派類型、 開始日期、 時間和結束日期選項](./media/pim-resource-roles-external-users/membership-settings.png)

1. 若要完成指派，請按一下 [完成]  ，然後 [新增]  。

    來賓角色指派會出現在您的角色清單。

    ![列出為合格客體的 [角色] 頁面](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>啟用以來賓身分的角色

身為外部使用者，必須先接受您的 Azure AD 組織的邀請，並可能啟用您的角色。

1. 開啟您的邀請電子郵件。 電子郵件會如下所示。

    ![目錄名稱、 個人訊息與開始使用連結的電子郵件邀請](./media/pim-resource-roles-external-users/email-invite.png)

1. 按一下電子郵件中的 [開始]  連結。

1. 檢閱權限之後，按一下 [接受]  。

    ![檢閱在瀏覽器中使用的組織想要檢閱您的權限清單的權限 頁面](./media/pim-resource-roles-external-users/invite-accept.png)

1. 可能會要求您接受使用規定，並指定您是否想要保持登入。

    Azure 入口網站隨即開啟。 如果您剛具備某個角色的資格，則沒有資源的存取權。

1. 若要啟用您的角色，請開啟含有啟用角色連結的電子郵件。 電子郵件會如下所示。

    ![從 PIM 指出該您符合資格的角色未啟用的角色連結的電子郵件訊息](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. 按一下 [啟用角色]  以在 PIM 中開啟您有資格的角色。

    ![我在列出您的合格角色的 PIM 中的 [角色] 頁面](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 按一下 [動作] 下方的 [啟用]  連結。

    根據角色設定，您必須指定一些資訊才能啟用該角色。

1. 您指定了角色的設定之後，按一下 [啟用]  以啟用角色。

    ![啟用網頁清單範圍及選項，以指定的開始時間、 持續時間和原因](./media/pim-resource-roles-external-users/activate-role.png)

    除非需要系統管理員核准您的要求，否則您應該具有所指定資源的存取權。

## <a name="view-activity-for-a-guest"></a>檢視活動的來賓

就像成員使用者，您可以檢視稽核記錄來追蹤來賓在做什麼。

1. 身為管理員，開啟 PIM 並選取已共用的資源。

1. 按一下 [資源稽核]  來檢視該資源的活動。 下列範例顯示資源群組的活動範例。

    ![Azure 資源-資源稽核網頁列出時間、 要求者和動作](./media/pim-resource-roles-external-users/audit-resource.png)

1. 若要檢視客體的活動，請按一下**Azure Active Directory** > **使用者**> 客體名稱。

1. 按一下 **稽核記錄檔**以查看組織的稽核記錄檔。 如有需要，您可以搜尋篩選條件。

    ![目錄稽核記錄清單的日期、 目標，由和活動](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>後續步驟

- [指派 Azure AD PIM 中的系統管理員角色](pim-how-to-add-role-to-user.md)
- [什麼是 Azure Active Directory B2B 中的來賓使用者存取權？](../b2b/what-is-b2b.md)
