---
title: 在 Azure AD 權利管理 (預覽) 中編輯和管理現有的存取套件-Azure Active Directory
description: 瞭解如何在 Azure Active Directory 權利管理 (預覽) 中編輯和管理現有的存取套件。
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: c07f9dbcc21840bec4b4487225dfb8108586865e
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489194"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 權利管理 (預覽) 中編輯和管理現有的存取套件

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

存取封裝可讓您進行一次性的資源和原則設定, 以自動管理存取套件的存取權。 身為存取套件管理員, 您可以隨時變更存取套件中的資源, 而不必擔心布建使用者對新資源的存取權, 或從先前的資源中移除其存取權。 原則也可以隨時更新, 不過, 原則變更只會影響新的存取權。

本文說明如何編輯和管理現有的存取封裝。

## <a name="add-resource-roles"></a>新增資源角色

資源角色是與資源相關聯的許可權集合。 將資源角色新增至您的存取套件, 讓使用者可以要求資源的方式。 您可以加入群組、應用程式和 SharePoint 網站的資源角色。

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 在左側功能表中, 按一下 [**資源角色**]。

1. 按一下 [**新增資源角色**] 以開啟 [新增資源角色以存取封裝] 頁面。

    ![存取套件-新增資源角色](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. 根據您要新增群組、應用程式或 SharePoint 網站而定, 請執行下列其中一個資源角色章節中的步驟。

### <a name="add-a-group-resource-role"></a>新增群組資源角色

當使用者獲指派存取套件時, 您可以讓 [權利管理] 自動將使用者新增至群組。 

- 當群組屬於存取套件的一部分, 且使用者被指派至該存取套件時, 使用者就會新增至該群組 (如果尚未存在的話)。
- 當使用者的存取套件指派過期時, 除非他們目前有指派給另一個包含該相同群組的存取套件, 否則會從群組中移除。

您可以選取任何 Office 365 群組或 Azure AD 安全性群組。  系統管理員可以將任何群組新增至目錄;如果目錄擁有者是群組的擁有者, 則可以將任何群組新增至目錄。 選取群組時, 請記住下列 Azure AD 條件約束:

- 當使用者 (包括來賓) 新增為群組的成員時, 他們可以看到該群組的所有其他成員。
- Azure AD 無法變更使用 Azure AD Connect 從 Windows Server Active Directory 同步處理之群組的成員資格。  
- 藉由新增或移除成員, 無法更新動態群組的成員資格, 因此動態群組成員資格不適合與權利管理搭配使用。

1. 在 [**新增資源角色以存取封裝**] 頁面上, 按一下 [**群組**] 以開啟 [選取群組] 窗格。

1. 選取您想要包含在存取套件中的群組。

    ![存取套件-新增資源角色-選取群組](./media/entitlement-management-access-package-edit/group-select.png)

1. 按一下 [選取] 。

1. 在 [**角色**] 清單中, 選取 [**擁有**者] 或 [**成員**]。

    您通常會選取成員角色。 如果您選取 [擁有者] 角色, 則可讓使用者新增或移除其他成員或擁有者。

    ![存取套件-新增群組的資源角色](./media/entitlement-management-access-package-edit/group-role.png)

1. 按一下 [新增] 。

    新增至存取套件的任何使用者都會自動成為此群組的成員。

### <a name="add-an-application-resource-role"></a>新增應用程式資源角色

當使用者獲指派存取套件時, 您可以讓 Azure AD 自動指派 Azure AD 企業應用程式的存取權給使用者, 包括 SaaS 應用程式以及與 Azure AD 同盟的組織應用程式。 針對透過同盟單一登入與 Azure AD 整合的應用程式, Azure AD 會為指派給應用程式的使用者發出同盟權杖。

應用程式可以有多個角色。 將應用程式新增至存取套件時, 如果該應用程式有一個以上的角色, 您必須為這些使用者指定適當的角色。  如果您正在開發應用程式, 您可以在如何[設定 SAML 權杖中發出的角色](../develop/active-directory-enterprise-app-role-management.md)宣告一文中, 深入瞭解如何將這些角色提供給您的應用程式。

一旦應用程式角色是存取套件的一部分:

- 當使用者獲指派該存取套件時, 會將使用者新增至該應用程式角色 (如果尚未存在的話)。
- 當使用者的存取套件指派過期時, 將會從應用程式中移除其存取權, 除非他們有指派給另一個包含該應用程式角色的存取封裝。

以下是選取應用程式時的一些考慮:

- 應用程式也可能會有指派給其角色的群組。  您可以選擇新增群組來取代存取套件中的應用程式角色, 但是在我的存取權入口網站中, 使用者將看不到該應用程式作為存取套件的一部分。

1. 在 [**新增資源角色以存取封裝**] 頁面上, 按一下 [**應用程式**] 以開啟 [選取應用程式] 窗格。

1. 選取您想要包含在存取套件中的應用程式。

    ![存取套件-新增資源角色-選取應用程式](./media/entitlement-management-access-package-edit/application-select.png)

1. 按一下 [選取] 。

1. 在 [**角色**] 清單中, 選取應用程式角色。

    ![存取套件-新增應用程式的資源角色](./media/entitlement-management-access-package-edit/application-role.png)

1. 按一下 [新增] 。

    在新增此應用程式時, 任何具有存取封裝之現有指派的使用者都會自動獲得存取權。

### <a name="add-a-sharepoint-site-resource-role"></a>新增 SharePoint 網站資源角色

當使用者獲指派存取套件時, Azure AD 可以自動指派 SharePoint Online 網站或 SharePoint Online 網站集合的存取權。

1. 在 [**將資源角色新增至存取封裝**] 頁面上, 按一下 [ **sharepoint 網站**] 以開啟 [選取 sharepoint Online 網站] 窗格。

1. 選取您想要包含在存取套件中的 SharePoint Online 網站。

    ![存取套件-新增資源角色-選取 SharePoint Online 網站](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. 按一下 [選取] 。

1. 在 [**角色**] 清單中, 選取 SharePoint Online 網站角色。

    ![存取套件-新增 SharePoint Online 網站的資源角色](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. 按一下 [新增] 。

    新增存取套件的任何使用者, 將會自動獲得此 SharePoint Online 網站的存取權。

## <a name="remove-resource-roles"></a>移除資源角色

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 在左側功能表中, 按一下 [**資源角色**]。

1. 在資源角色清單中, 尋找您想要移除的資源角色。

1. 按一下省略號 ( **...** ), 然後按一下 [**移除資源角色**]。

    已移除存取套件之現有指派的任何使用者, 將會自動在此資源角色中撤銷其存取權。

## <a name="add-a-new-policy"></a>新增原則

您指定誰可以要求存取套件的方式是建立原則。 如果您想要允許不同組使用者被授與不同核准和到期設定的指派, 您可以為單一存取封裝建立多個原則。 單一原則不能用來將內部和外部使用者指派給相同的存取封裝。 不過, 您可以在相同的存取套件中建立兩個原則, 一個用於內部使用者, 另一個用於外部使用者。 如果有多個套用至使用者的原則, 則會在其要求選取要指派的原則時提示他們。

下圖顯示為現有存取套件建立原則的高階程式。

![建立原則處理常式](./media/entitlement-management-access-package-edit/policy-process.png)

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 依序按一下 [**原則**] 和 [**新增原則**]。

1. 輸入原則的 [名稱] 和 [描述]。

    ![建立具有名稱和描述的原則](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. 根據您對於**可以要求存取權的使用者**所做的選擇, 執行下列其中一個原則區段中的步驟。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>編輯現有的原則

您可以隨時編輯原則。 如果您變更原則的到期日, 已處於 [待核准] 或 [已核准] 狀態之要求的到期日將不會變更。

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 按一下 [**原則**], 然後按一下您想要編輯的原則。

    [**原則詳細資料**] 窗格會在頁面底部開啟。

    ![存取套件-原則詳細資料窗格](./media/entitlement-management-access-package-edit/policy-details.png)

1. 按一下 [**編輯**] 以編輯原則。

    ![存取套件-編輯原則](./media/entitlement-management-access-package-edit/policy-edit.png)

1. 完成後, 按一下 [**更新**]。

## <a name="directly-assign-a-user"></a>直接指派使用者

在某些情況下, 您可能會想要將特定使用者直接指派給存取套件, 讓使用者不必完成要求存取套件的程式。 若要直接指派使用者, 存取套件必須具有允許系統管理員直接指派的原則。

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 在左側功能表中, 按一下 [**指派**]。

1. 按一下 [**新增指派**] 以開啟 [將使用者新增至存取套件]。

    ![指派-將使用者新增至存取封裝](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. 按一下 [**新增使用者**] 以選取您想要指派存取套件的使用者。

1. 在 [**選取原則**] 清單中, 選取具有 [[無 (僅限系統管理員直接指派)](#policy-none-administrator-direct-assignments-only) ] 設定的原則。

    如果此存取套件沒有這種類型的原則, 您可以按一下 [**建立新原則**] 來加入一個。

1. 設定您想要選取的使用者指派開始和結束的日期和時間。 如果未提供結束日期, 則會使用原則的到期設定。

1. 選擇性地為記錄保留的直接指派提供理由。

1. 按一下 [**新增**], 將選取的使用者直接指派給存取封裝。

    幾分鐘後, 按一下 [ 重新整理] 以查看 [指派] 清單中的使用者。

## <a name="view-who-has-an-assignment"></a>查看誰具有指派

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 按一下 [**指派**], 以查看作用中的指派清單。

1. 按一下特定的指派以查看其他詳細資料。

1. 若要查看未正確布建所有資源角色的指派清單, 請按一下 [篩選狀態], 然後選取 [**傳遞**]。

    您可以在 [**要求**] 頁面上尋找使用者的對應要求, 以查看傳遞錯誤的其他詳細資料。

1. 若要查看過期的指派, 請按一下篩選狀態, 然後選取 [已**過期**]。

1. 若要下載已篩選清單的 CSV 檔案, 請按一下 [**下載**]。

## <a name="view-requests"></a>View 要求

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 按一下特定要求以查看其他詳細資料。

## <a name="view-a-requests-delivery-errors"></a>查看要求的傳遞錯誤

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 選取您想要查看的要求。

    如果要求有任何傳遞錯誤, 要求狀態將會是 [ 未傳遞], 而子狀態將會**部分傳遞**。

    如果有任何傳遞錯誤, 在要求的詳細資料窗格中, 將會有傳遞錯誤的計數。

1. 按一下 [計數], 以查看所有要求的傳遞錯誤。

## <a name="cancel-a-pending-request"></a>取消擱置要求

您只能取消尚未傳遞的暫止要求。

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 按一下 [**要求**]。

1. 按一下您要取消的要求

1. 在 [要求詳細資料] 窗格中, 按一下 [**取消要求**]。

## <a name="copy-my-access-portal-link"></a>複製我的存取權入口網站連結

您目錄中的大部分使用者都可以登入我的存取權入口網站, 並自動查看他們可以要求的存取套件清單。 不過, 對於還不在您目錄中的外部商業夥伴使用者, 您將需要傳送一個連結, 讓他們可以用來要求存取封裝。 只要針對外部使用者啟用存取套件, 而且您有外部使用者目錄的原則, 外部使用者就可以使用我的存取權入口網站連結來要求存取套件。

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 在 [總覽] 頁面上, 複製 [**我的存取權入口網站] 連結**。

    ![存取套件概觀 - 我的存取權入口網站的連結](./media/entitlement-management-shared/my-access-portal-link.png)

1. 傳送電子郵件給您的外部商業夥伴, 或將其連結。 他們可以與使用者共用連結, 以要求存取封裝。

## <a name="change-the-hidden-setting"></a>變更隱藏的設定

根據預設, 存取封裝是可探索的。 這表示如果原則允許使用者要求存取套件, 他們會自動看到其我的存取權入口網站中所列的存取套件。

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 在 [總覽] 頁面上, 按一下 [**編輯**]。

1. 設定**隱藏**的設定。

    如果設定為 [**否**], 存取套件將會列在使用者的我的存取權入口網站中。

    如果設定為 **[是]** , 存取套件就不會列在使用者的我的存取權入口網站中。 使用者可以查看存取套件的唯一方式, 就是他們擁有存取套件的直接**我的存取權入口網站連結**。

## <a name="delete"></a>DELETE

只有在沒有作用中的使用者指派時, 才能刪除存取封裝。

**必要角色：** 使用者系統管理員、目錄擁有者或存取套件管理員

1. 在 Azure 入口網站中按一下 [Azure Active Directory]，然後按一下 [身分識別治理]。

1. 在左側功能表中, 按一下 [**存取套件**], 然後開啟存取封裝。

1. 在左側功能表中, 按一下 [**指派**], 並移除 [所有使用者的存取權]。

1. 在左側功能表中, 按一下 **[總覽**], 然後按一下 [**刪除**]。

1. 在出現的 [刪除] 訊息中, 按一下 [**是]** 。

## <a name="when-are-changes-applied"></a>套用變更的時機

在 [權利管理] 中, Azure AD 會一天多次處理存取套件中指派和資源的大量變更。 因此, 如果您進行指派或變更存取套件的資源角色, 可能需要24小時的時間才能在 Azure AD 中進行該變更, 加上將這些變更傳播到其他 Microsoft Online Services 或已連線 SaaS 應用程式所需的時間量今日. 如果您的變更只會影響幾個物件, 則變更在 Azure AD 中可能只需要幾分鐘的時間, 之後其他 Azure AD 元件就會偵測到該變更並更新 SaaS 應用程式。 如果您的變更會影響數以千計的物件, 變更將會花費較長的時間。 例如, 如果您有一個具有2個應用程式和100使用者指派的存取套件, 而且您決定將 SharePoint 網站角色加入至存取封裝, 則在所有使用者都屬於該 SharePoint 網站角色的一部分之前, 可能會有延遲。 您可以透過 Azure AD 的 audit 記錄檔、Azure AD 布建記錄檔和 SharePoint 網站 audit 記錄來監視進度。

## <a name="next-steps"></a>後續步驟

- [新增類別目錄擁有者或存取套件管理員](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [要求處理和電子郵件通知](entitlement-management-process.md)
