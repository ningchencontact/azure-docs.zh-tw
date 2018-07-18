---
title: 在 Azure Active Directory 中新增或刪除使用者 | Microsoft Docs
description: 說明如何在 Azure Active Directory 中新增使用者或刪除現有的使用者
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: e6e21ea09909a3bd92a21e15428af347e3f20b93
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37767355"
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>快速入門：將使用者新增至 Azure Active Directory
本文說明如何在組織的 Azure Active Directory (Azure AD) 租用戶中刪除或新增您組織的使用者，而方法是使用 Azure 入口網站，或同步處理內部部署 Windows Server AD 使用者帳戶資料。 

## <a name="add-cloud-based-users"></a>新增雲端式使用者
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 選取 [Azure Active Directory]，然後選取 [使用者和群組]。
3. 在 [使用者和群組] 上，選取 [所有使用者]，然後選取 [新增使用者]。
   ![選取 [新增] 命令](./media/add-users-azure-active-directory/add-user.png)
4. 輸入使用者的詳細資料，例如「名稱」和「使用者名稱」。 使用者名稱的網域名稱部分必須是初始預設網域名稱 "[網域名稱].onmicrosoft.com"，或是已驗證的非同盟[自訂網域名稱](add-custom-domain.md)，例如"contoso.com"。
5. 複製或記下產生的使用者密碼，以便在此程序完成後，將它提供給使用者。
6. (選擇性) 您可以開啟使用者的 [設定檔]、[群組] 或 [目錄角色]，然後在其中填入資訊。 如需有關使用者和系統管理員角色的詳細資訊，請參閱 [在 Azure AD 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)。
7. 在 [使用者] 中，選取 [建立]。
8. 將產生的密碼安全地散發給新使用者，以便讓使用者可以登入。

> [!TIP]
> 您也可以同步處理內部部署 Windows Server AD 中的使用者帳戶資料。 Microsoft 的身分識別解決方案可跨越內部部署和雲端架構功能，建立單一使用者身分識別以用於所有資源的驗證和授權，不論位於何處。 我們稱之為混合式身分識別。 您可以針對混合式身分識別案例，使用 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 來整合您的內部部署目錄與 Azure Active Directory。 這可讓您為與 Azure AD 整合之 Office 365、Azure 和 SaaS 應用程式的使用者提供通用身分識別。 

## <a name="delete-users-from-azure-ad"></a>從 Azure AD 刪除使用者
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組]。
3. 在 [使用者和群組] 刀鋒視窗上，從清單中選取要刪除的使用者。 
4. 在所選使用者的刀鋒視窗上選取 [概觀]，然後在命令列中選取 [刪除]。
   ![選取 [新增] 命令](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>深入了解 
* [從另一個目錄中新增來賓使用者](../b2b/what-is-b2b.md) 
* [在 Azure AD 中將使用者指派給角色](active-directory-users-assign-role-azure-portal.md)
* [管理使用者設定檔](active-directory-users-profile-azure-portal.md)
* [還原已刪除的使用者](active-directory-users-restore.md)



## <a name="next-steps"></a>後續步驟
在此快速入門中，您學到如何將使用者新增至 Azure AD Premium。 

您可以使用下列連結，從 Azure 入口網站在 Azure AD 中建立新的使用者。

>[!div class="nextstepaction"]
>[在 Azure AD 中新增使用者](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/)