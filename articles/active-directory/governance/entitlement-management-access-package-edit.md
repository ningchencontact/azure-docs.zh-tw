---
title: 編輯和管理 Azure AD 權限管理 （預覽）-Azure Active Directory 中現有的存取封裝
description: 了解如何編輯和管理 Azure Active Directory 權限管理 （預覽） 中的現有存取套件。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce51f4df50de50cef06bba699ca7c6f76bc5d166
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833149"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>編輯和管理 Azure AD 權限管理 （預覽） 中的現有存取套件

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 權利管理目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

存取封裝可讓您執行自動管理存取套件的生命週期的存取權的資源和原則的單次安裝。 為存取套件管理員 中，您可以隨時變更存取封裝中的資源而不需擔心佈建至新的資源，使用者的存取，或從先前的資源中移除其存取權。 原則也可以在任何時候更新，不過，原則變更只會影響新的存取。

本文說明如何編輯和管理現有的存取封裝。

## <a name="add-resource-roles"></a>新增資源角色

資源的角色是與資源相關聯的權限的集合。 您會將資源可供使用者要求的方式是將資源角色新增到您的存取封裝。 您可以新增資源群組、 應用程式和 SharePoint 網站的角色。

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 在左側功能表中，按一下**資源角色**。

1. 按一下 **新增資源角色**若要開啟 新增資源角色，若要存取封裝頁面。

    ![存取封裝-新增資源角色](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. 根據您要新增群組、 應用程式或 SharePoint 網站，請在下列的資源角色區段中的一個執行步驟。

### <a name="add-a-group-resource-role"></a>新增群組的資源角色

您可以自動將使用者新增至群組，會在指派存取封裝時的權限管理。 

- 當群組是存取套件和部分使用者指派給該存取封裝中，使用者新增至該群組中，如果尚不存在。
- 當使用者的存取封裝指派到期時，它們會從群組中移除，除非目前其中有指派給另一個包含該相同群組的存取封裝。

您可以選取任何 Office 365 群組] 或 [Azure AD 安全性群組。  系統管理員可以將任何群組加入至目錄中;類別目錄的擁有者可以將任何群組加入至目錄，如果它們是群組擁有者。 選取群組時，請記住下列的 Azure AD 條件約束：

- 當使用者，包括來賓，新增為成員的群組時，他們會看到該群組的所有其他成員。
- Azure AD 無法變更 Windows Server Active Directory 使用 Azure AD Connect 同步處理群組的成員資格。  
- 動態群組的成員資格，請新增或移除成員，因此不適合搭配權利管理動態群組成員資格無法更新。

1. 在 **加入資源的角色，以存取封裝**頁面上，按一下**群組**以開啟 選取群組 窗格。

1. 選取您想要存取封裝中包含的群組。

    ![存取封裝-新增資源角色-選取的群組](./media/entitlement-management-access-package-edit/group-select.png)

1. 按一下 [選取] 。

1. 在 **角色**清單中，選取**擁有者**或是**成員**。

    您通常會選取成員的角色。 如果您選取的擁有者角色，可讓使用者加入或移除其他成員或擁有者。

    ![存取封裝-新增之群組的資源角色](./media/entitlement-management-access-package-edit/group-role.png)

1. 按一下 [新增] 。

    使用現有的指派，以存取套件的任何使用者加入時，會自動成為此群組的成員。

### <a name="add-an-application-resource-role"></a>新增應用程式資源角色

您可以自動將使用者指派存取權給 Azure AD 企業應用程式，包括 SaaS 應用程式和將使用者指派存取套件的 Azure AD 同盟的組織的應用程式的 Azure AD。 對於與透過同盟單一登入 Azure AD 整合的應用程式，Azure AD 會發出使用者指派給應用程式的同盟權杖。

應用程式可以有多個角色。 當該應用程式有多個角色，請新增至存取套件，應用程式，您必須指定適當的角色，這些使用者。  如果您正在開發的應用程式，您可以閱讀更多有關這些角色可供如何 」 文件中的應用程式[設定 SAML 權杖中發出的角色宣告](../develop/active-directory-enterprise-app-role-management.md)。

一旦應用程式角色是存取套件的一部分：

- 當將使用者指派存取套件時，使用者會新增至該應用程式角色，如果尚不存在。
- 當使用者的存取封裝指派到期時，其存取權會移除應用程式，除非他們擁有指派給另一個包含該應用程式角色的存取封裝。

選取應用程式時，以下是一些考量：

- 應用程式可能也必須指派給其角色群組。  您可以選擇將群組加入取代應用程式角色中的存取套件，不過，則應用程式將不會顯示給使用者在我存取入口網站中存取套件的一部分。

1. 在 **加入資源的角色，以存取套件**頁面上，按一下**應用程式**以開啟 選取應用程式 窗格。

1. 選取您想要存取封裝中包含的應用程式。

    ![存取封裝-新增資源角色-選取應用程式](./media/entitlement-management-access-package-edit/application-select.png)

1. 按一下 [選取] 。

1. 在 **角色**清單中，選取 應用程式角色。

    ![存取封裝-新增應用程式的資源角色](./media/entitlement-management-access-package-edit/application-role.png)

1. 按一下 [新增] 。

    使用現有的指派，以存取套件的任何使用者會自動獲得存取此應用程式加入時。

### <a name="add-a-sharepoint-site-resource-role"></a>新增 SharePoint 站台資源角色

Azure AD 可以自動將使用者指派存取 SharePoint Online 網站或 SharePoint Online 網站集合會在指派存取封裝時。

1. 在 **加入資源的角色，以存取套件**頁面上，按一下**SharePoint 網站**以開啟 選取的 SharePoint Online 的站台 窗格。

1. 選取您想要存取封裝中包含 SharePoint Online 網站。

    ![存取封裝-新增資源角色-選取 SharePoint Online 網站](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. 按一下 [選取] 。

1. 在 **角色**清單中，選取 SharePoint Online 的站台角色。

    ![存取封裝-新增 SharePoint Online 網站的資源角色](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. 按一下 [新增] 。

    使用現有的指派，以存取套件的任何使用者會自動獲得存取此 SharePoint Online 網站加入時。

## <a name="remove-resource-roles"></a>移除資源角色

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 在左側功能表中，按一下**資源角色**。

1. 資源角色清單中，找出您想要移除的資源角色。

1. 按一下省略符號 (**...**)，然後按一下 **移除資源角色**。

    使用現有的指派，以存取套件的任何使用者會自動在撤銷此資源角色時就會移除其存取權。

## <a name="add-a-new-policy"></a>新增原則

您指定誰可以要求存取套件的方式是建立原則。 如果您想要允許不同的使用者若要獲得指派不同的核准與到期日設定，您可以建立單一存取封裝的多個原則。 單一原則不能將內部和外部使用者指派給相同的存取封裝。 不過，您可以建立兩個原則，在相同的存取套件，一個用於內部使用者，一個用於外部使用者。 如果有多個原則套用至使用者，系統會在他們想要指派給選取的原則要求的時間。

下圖顯示建立的原則，現有的存取封裝的高階程序。

![建立原則的程序](./media/entitlement-management-access-package-edit/policy-process.png)

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 按一下 **原則**，然後**新增原則**。

1. 輸入名稱和原則的描述。

    ![建立原則，使用名稱和描述](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. 根據您的選取項目，如**可以要求存取使用者**，執行其中一個下列的原則區段中的步驟。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>編輯現有的原則

您可以隨時編輯原則。 如果您變更原則的到期日時，不會變更已暫止的核准或已核准狀態的要求的到期日。

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 按一下 **原則**，然後按一下您想要編輯的原則。

    **原則的詳細資訊**窗格底部的頁面會隨即開啟。

    ![存取封裝-原則詳細資料窗格](./media/entitlement-management-access-package-edit/policy-details.png)

1. 按一下 **編輯**編輯原則。

    ![存取封裝-編輯原則](./media/entitlement-management-access-package-edit/policy-edit.png)

1. 完成後，按一下**更新**。

## <a name="directly-assign-a-user"></a>將使用者直接指派

在某些情況下，您可能想要直接將特定使用者指派給存取套件，讓使用者不必經過要求存取套件的程序。 若要直接指派使用者，請存取套件必須可讓系統管理員直接指派的原則。

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 在左側功能表中，按一下**指派**。

1. 按一下 **新的指派**若要開啟 將使用者新增至存取套件。

    ![指派-新增使用者來存取封裝](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. 按一下 **將使用者新增**來選取您想要指派存取封裝的使用者。

1. 在 **選取原則**清單中，選取的原則[None （系統管理員直接指派只）](#policy-none-administrator-direct-assignments-only)設定。

    如果此存取套件並沒有這種類型的原則，您可以按一下**建立新的原則**新增一個。

1. 設定您想要選取的使用者指派為開始和結束的日期和時間。 如果未提供的結束日期，則將使用該原則的到期設定。

1. 選擇性地提供理由來進行記錄保存程式直接指派。

1. 按一下 **新增**，將選取的使用者直接指派給存取封裝。

    幾分鐘後，按一下**重新整理**看到指派清單中的使用者。

## <a name="view-who-has-an-assignment"></a>檢視指派的人員

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 按一下 **指派**若要查看作用中的指派清單。

1. 按一下 特定的指派，以查看其他詳細資料。

1. 若要查看一份沒有正確佈建的所有資源角色的指派，請按一下篩選器狀態，然後選取**傳遞**。

    您也可以尋找使用者的對應要求上傳遞錯誤查看其他詳細資料**要求**頁面。

1. 若要查看已過期的指派，請按一下篩選器狀態，然後選取**已過期**。

1. 若要下載 CSV 檔案的已篩選的清單，請按一下**下載**。

## <a name="view-requests"></a>檢視要求

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 按一下 **要求**。

1. 按一下特定的要求，以查看其他詳細資料。

## <a name="view-a-requests-delivery-errors"></a>檢視要求的傳遞錯誤

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 按一下 **要求**。

1. 選取您想要檢視的要求。

    如果要求具備任何傳遞錯誤，將會要求狀態**Undelivered**作業，而且子狀態**部分傳遞**。

    如果有任何傳遞錯誤，要求的詳細資料窗格中，則會傳遞錯誤的計數。

1. 按一下要查看所有的要求傳遞錯誤的計數。

## <a name="cancel-a-pending-request"></a>取消擱置要求

您只能取消尚未傳遞的暫止要求。

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 按一下 **要求**。

1. 按一下您想要取消的要求

1. 在 [要求詳細資料] 窗格中，按一下**取消要求**。

## <a name="copy-my-access-portal-link"></a>複製我存取入口網站的連結

您的目錄中的大部分使用者可以登入我存取入口網站，並會自動看到他們可以要求存取套件的清單。 不過，尚無法在目錄中的外部公司夥伴使用者，您必須將它們傳送它們可用來要求存取套件的連結。 只要存取封裝已啟用外部使用者，而且您有外部使用者的目錄中的原則，外部使用者可以使用我存取入口網站 連結來要求存取套件。

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 在 [概觀] 頁面上複製**我存取入口網站連結**。

    ![存取封裝的概觀-我存取入口網站的連結](./media/entitlement-management-shared/my-access-portal-link.png)

1. 電子郵件，或將此連結傳送給外部公司夥伴。 他們可以與使用者要求存取套件來共用連結。

## <a name="change-the-hidden-setting"></a>變更 [隱藏] 設定

存取套件是可探索的預設值。 這表示如果原則可讓使用者要求存取套件，就會自動看到列在其我存取入口網站中存取套件。

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 在 概觀 頁面中，按一下 **編輯**。

1. 設定**Hidden**設定。

    如果設定為**No**，存取套件將會列在使用者的我存取入口網站。

    如果設定為**是**，存取套件不會列在入口網站中我的存取權的使用者。 使用者可以檢視存取套件的唯一方式是它們是否有直接**我存取入口網站連結**存取套件。

## <a name="delete"></a>Delete

如果不有任何作用中的使用者指派，可以只刪除存取套件。

**必要的角色：** 使用者管理員、 類別目錄的擁有者或存取套件管理員

1. 在 Azure 入口網站中，按一下**Azure Active Directory** ，然後按一下**身分識別控管**。

1. 在左側功能表中，按一下**存取套件**，然後開啟 存取套件。

1. 在左側功能表中，按一下**指派**並移除所有使用者的存取權。

1. 在左側功能表中，按一下**概觀**，然後按一下**刪除**。

1. 在出現的刪除訊息，按一下**是**。

## <a name="when-are-changes-applied"></a>當套用變更

在權限管理，Azure AD 將會處理指派和資源存取封裝中的大量變更一天多次。 因此，如果您進行指派，或變更您的存取封裝的資源角色時，可能需要 24 小時，要在 Azure AD 中進行這項變更，再加上的時間量它會將這些變更傳播至其他 Microsoft Online Services 或連接的 SaaS 應用程式s。 如果您的變更會影響少數的物件，變更可能只需要幾分鐘才能套用在 Azure AD 中之後，其他 Azure AD 的元件會再偵測到的變更，並更新的 SaaS 應用程式。 如果您的變更會影響數千個物件，變更將會延長。 比方說，如果您有 2 個應用程式和 100 的使用者指派存取套件，而且您決定要將 SharePoint 網站角色新增至存取套件，可能會延遲直到所有的使用者都該 SharePoint 站台角色的一部分。 您可以監視透過 Azure AD 稽核記錄、 Azure AD 佈建記錄檔，以及 SharePoint 網站的稽核記錄檔的進度。

## <a name="next-steps"></a>後續步驟

- [要求處理程序與電子郵件通知](entitlement-management-process.md)
