---
title: 開始使用 PIM-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure 入口網站中啟用及開始使用 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408991ffc3922986234f7d40e1cd589b1d126ba1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476477"
---
# <a name="start-using-pim"></a>開始使用 PIM

您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 來管理、控制和監視組織內的存取。 此範圍包括存取 Azure 資源、Azure AD 和其他 Microsoft 線上服務 (例如 Office 365 或 Microsoft Intune)。

本文說明如何啟用及開始使用 PIM。

## <a name="prerequisites"></a>必要條件

若要使用 PIM，您必須具有下列其中一個授權：

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

如需詳細資訊，請參閱[使用 PIM 的授權需求](subscription-requirements.md)。

## <a name="first-person-to-use-pim"></a>使用 PIM 的第一個人

如果您是目錄中使用 PIM 的第一人，則會自動獲指派目錄中的[安全性系統管理員](../users-groups-roles/directory-assign-admin-roles.md#security-administrator)和[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色。 只有特殊權限的角色系統管理員可以管理 Azure AD 角色指派的使用者。 此外，您也可以選擇執行[安全性精靈](pim-security-wizard.md)，讓其引導您完成初始的探索和指派體驗。

## <a name="enable-pim"></a>啟用 PIM

若要在目錄中開始使用 PIM，您必須先啟用 PIM。

1. 以目錄的全域系統管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。

    您必須是具有組織帳戶 (例如 @yourdomain.com) 而非 Microsoft 帳戶 (例如 @outlook.com) 的全域管理員，才能啟用目錄的 PIM。

1. 按一下 [所有服務]  ，並尋找 [Azure AD Privileged Identity Management]  服務。

    ![所有服務中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 按一下以開啟 PIM 快速入門。

1. 在清單中，按一下 [同意 PIM]  。

    ![同意 PIM，若要啟用 PIM](./media/pim-getting-started/consent-pim.png)

1. 按一下 [驗證我的身分識別]  以使用 Azure MFA 驗證您的身分識別。 系統會要求您選取帳戶。

    ![挑選帳戶視窗，以確認您的身分識別](./media/pim-getting-started/pick-account.png)

1. 如果需要更多資訊才能完成驗證，系統將會引導您完成相關程序。 如需詳細資訊，請參閱[取得雙步驟驗證的說明](https://go.microsoft.com/fwlink/p/?LinkId=708614)。

    ![如果您的組織需要更多的資訊視窗所需的詳細資訊](./media/pim-getting-started/more-information-required.png)

    例如，系統可能會要求您提供電話驗證。

    ![詢問如何與您連絡其他安全性驗證 頁面](./media/pim-getting-started/additional-security-verification.png)

1. 一旦您完成驗證程序後，請按一下 [同意]  按鈕。

1. 在出現的訊息中，按一下 [是]  以同意 PIM 服務。

    ![同意 PIM 訊息，必須先完成同意程序](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>針對 Azure AD 角色註冊 PIM

為目錄啟用 PIM 後，您必須註冊 PIM 來管理 Azure AD 角色。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure AD 角色]  。

    ![針對 Azure AD 角色註冊 PIM](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. 按一下 [註冊]  。

1. 在出現的訊息中，按一下 [是]  以註冊 PIM 來管理 Azure AD 角色。

    ![針對 Azure AD 角色註冊 PIM 的訊息](./media/pim-getting-started/sign-up-pim-message.png)

    完成註冊時，將會啟用 Azure AD 選項。 您可能需要重新整理入口網站。

    若要了解如何探索及選取要以 PIM 保護的 Azure 資源，請參閱[在 PIM 中探索要管理的 Azure 資源](pim-resource-roles-discover-resources.md)。

## <a name="navigate-to-your-tasks"></a>瀏覽至您的工作

PIM 設定完成後，您可以執行身分識別管理工作。

![瀏覽視窗中的 PIM 顯示工作，並管理選項](./media/pim-getting-started/pim-quickstart-tasks.png)

| 工作 + 管理 | 描述 |
| --- | --- |
| **我的角色**  | 顯示一個清單，列出已指派給您的合格和使用中角色。 您可以在這裡啟動任何指派的合格角色。 |
| **我的要求** | 顯示您啟動合格角色指派的任何擱置要求。 |
| **核准要求** | 顯示一個清單，列出您目錄中的使用者為了啟動合格角色所提出的要求，而此目錄是指定給您以進行核准的目錄。 |
| **檢閱存取** | 列出指派給您完成的使用中存取權檢閱 (無論您是在檢閱自己還是他人的存取權)。 |
| **Azure AD 角色** | 會顯示在儀表板和特殊權限的角色管理員用來管理 Azure AD 角色指派的設定。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的檢視] 儀表板只會顯示存取儀表板的使用者，而非整個租用戶的相關資訊。 |
| **Azure 資源** | 顯示儀表板和設定，讓特殊權限角色管理員可管理 Azure 資源角色指派。 對於任何不是特殊權限角色管理員的人員，系統會停用此儀表板。 這些使用者可以存取標題為 [我的檢視] 的特殊儀表板。 [我的檢視] 儀表板只會顯示存取儀表板的使用者，而非整個租用戶的相關資訊。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>將 PIM 圖格新增至儀表板

若要更輕鬆地開啟 PIM，您應將 PIM 圖格新增至 Azure 入口網站儀表板。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 按一下 [所有服務]  ，並尋找 [Azure AD Privileged Identity Management]  服務。

    ![所有服務中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 按一下以開啟 PIM 快速入門。

1. 請參閱**將刀鋒視窗釘選到儀表板**，將 [PIM 快速入門] 刀鋒視窗釘選到儀表板上。

    ![圖釘圖示來 PIM 刀鋒視窗釘選到儀表板](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 儀表板上，您會看到像這樣的圖格：

    ![在 [儀表板上的 PIM 快速入門] 圖格](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>後續步驟

- [將 Azure AD PIM 中的角色指派](pim-how-to-add-role-to-user.md)
- [在 PIM 中探索要管理的 Azure 資源](pim-resource-roles-discover-resources.md)
