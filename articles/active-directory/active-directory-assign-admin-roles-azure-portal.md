---
title: 在 Azure Active Directory 中指派系統管理員角色 | Microsoft Docs
description: 系統管理員角色可以新增使用者、指派系統管理角色、重設使用者密碼、管理使用者授權或管理網域。 獲指派管理員角色的使用者對於貴公司訂閱的所有雲端服務，具有相同的權限。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/07/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 33facedd1f2b3e2330a2a89a38084d99234fbf2e
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346695"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中指派系統管理員角色

使用 Azure Active Directory (Azure AD) 時，您可以指定個別的系統管理員來執行不同的功能。 您可以在 Azure AD 入口網站中指定系統管理員以執行多種工作，例如新增或變更使用者、指派系統管理角色、重設使用者密碼、管理使用者授權，以及管理網域名稱等。

## <a name="details-about-the-global-administrator-role"></a>全域管理員角色的詳細資料
全域管理員可以存取所有系統管理功能。 註冊 Azure 訂用帳戶的人員預設會獲指派目錄的全域管理員角色。 只有全域管理員才能指派其他系統管理員角色。

## <a name="assign-or-remove-administrator-roles"></a>指派或移除系統管理員角色
若要了解如何將系統管理角色指派給 Azure Active Directory 中的使用者，請參閱[在 Azure Active Directory 中將使用者指派給系統管理員角色](active-directory-users-assign-role-azure-portal.md)。

## <a name="available-roles"></a>可用的角色
可用的系統管理員角色如下：

* **[應用程式系統管理員](#application-administrator)**：此角色中的使用者可以建立和管理企業應用程式、應用程式註冊和應用程式 Proxy 設定的所有層面。 此角色也會授與能力來同意委派的權限以及 Microsoft Graph 和 Azure AD Graph 以外的應用程式權限。 建立新的應用程式註冊或企業應用程式時，不會加入此角色的成員作為擁有者。

* **[應用程式開發人員](#application-developer)**：將 [使用者可以註冊應用程式] 設定設為 [否] 時，此角色中的使用者可以建立應用程式註冊。 將 [使用者可同意應用程式代表自己存取公司資料] 設定設為 [否] 時，此角色也允許成員代表他們自己同意。 建立新的應用程式註冊或企業應用程式時，不會加入此角色的成員作為擁有者。

* **[計費管理員](#billing-administrator)**：進行採購、管理訂用帳戶、管理支援票證，以及監控服務健全狀況。

* **[雲端應用程式系統管理員](#cloud-application-administrator)**：此角色中的使用者具有與應用程式系統管理員角色相同的權限，但不包括管理應用程式 Proxy 的能力。 此角色會授與能力來建立和管理企業應用程式和應用程式註冊的所有層面。 此角色也會授與能力來同意委派的權限以及 Microsoft Graph 和 Azure AD Graph 以外的應用程式權限。 建立新的應用程式註冊或企業應用程式時，不會加入此角色的成員作為擁有者。

* **[規範管理員](#compliance-administrator)**：此角色的使用者擁有 Office 365 安全性與法規遵循中心和 Exchange 系統管理中心。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **[條件式存取系統管理員](#conditional-access-administrator)**：具有此角色的使用者能夠管理 Azure Active Directory 條件式存取設定。
  > [!NOTE]
  > 若要在 Azure 中部署 Exchange ActiveSync 條件式存取原則，使用者也必須是全域系統管理員。
  
* **[Dynamics 365 服務管理員/CRM 服務管理員](#crm-service-administrator)**︰具備此角色的使用者在有 Microsoft CRM Online 服務時，於該服務內具有全域權限，以及管理支援票證和監控服務健康情況的能力。 如需詳細資訊，請參閱[使用服務管理員角色管理您的租用戶](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。

* **[裝置系統管理員](#device-administrators)**︰具備此角色的使用者會在所有已加入 Azure Active Directory 的 Windows 10 裝置上成為本機電腦系統管理員。 它們並沒有在 Azure Active Directory 中管理裝置物件的能力。

* **[目錄讀取者](#directory-readers)**︰這是舊版角色，用來指派給不支援[同意架構](active-directory-integrating-applications.md)的應用程式。 不應將它指派給任何使用者。

* **[目錄同步作業帳戶](#directory-synchronization-accounts)**：請勿使用。 此角色會自動指派給 Azure AD Connect 服務，不適用於也不支援任何其他用途。

* **[目錄寫入者](#directory-writers)**︰這是舊版角色，用來指派給不支援[同意架構](active-directory-integrating-applications.md)的應用程式。 不應將它指派給任何使用者。

* **[Exchange 服務管理員](#exchange-service-administrator)**︰在有 Microsoft Exchange Online 服務時，具備此角色的使用者在該服務內會具有全域權限。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **[全域系統管理員 / 公司系統管理員](#company-administrator)**︰具有此角色的使用者可以存取 Azure Active Directory 中所有的系統管理功能，以及使用 Azure Active Directory 身分識別的服務，例如 Exchange Online、SharePoint Online 和 Skype for Business Online。 註冊 Azure Active Directory 租用戶的人員會變成全域管理員。 只有全域管理員才能指派其他系統管理員角色。 您的公司可以有多位全域管理員。 全域系統管理員可以為任何使用者和所有其他系統管理員重設密碼。

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 及 Azure AD PowerShell 中，是將此角色識別為「公司系統管理員」。 它是 [Azure 入口網站](https://portal.azure.com)中的「全域管理員」。
  >
  >

* **[來賓邀請者](#guest-inviter)**︰當 [可邀請成員] 使用者設定設為 [否] 時，此角色中的使用者可管理 Azure Active Directory B2B 來賓使用者邀請。 在[關於 Azure AD B2B 共同作業](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中查看 B2B 共同作業的詳細資訊。 這不包含任何其他權限。

* **[資訊保護系統管理員](#information-protection-administrator)**：具備此角色的使用者只在「Azure 資訊保護」服務上具有使用者權限。 此角色允許設定「Azure 資訊保護」原則的標籤、管理保護範本，以及啟用保護。 此角色並未授與「Identity Protection 中心」、Privileged Identity Management、「監視 Office 365 服務健康情況」及「Office 365 安全與規範中心」中的任何權限。

* **[Intune 服務管理員](#intune-service-administrator)**︰在有 Microsoft Intune Online 服務時，具備此角色的使用者在該服務內會具有全域權限。 此外，此角色包含管理使用者和裝置的能力，可相關聯原則以及建立和管理群組。 如需詳細資訊，請參閱[將角色型系統管理控制用於 Microsoft Intune](https://docs.microsoft.com/en-us/intune/role-based-access-control)

* **[訊息中心讀取者](#message-center-reader)**：此角色中的使用者可以在 [Office 365 訊息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)內，為他們的組織監視所設服務 (例如 Exchange、Intune 和 Microsoft Teams) 的通知和諮詢健康情況更新。 訊息中心讀者每週會收到貼文的電子郵件摘要和更新，並且可以在 Office 365 中分享訊息中心的貼文。 在 Azure AD 中，指派至此角色的使用者只會有 Azure AD 服務的唯讀存取權，與使用者和群組一樣。 

* **[合作夥伴第 1 層支援](#partner-tier1-support)**︰請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

* **[合作夥伴第 2 層支援](#partner-tier2-support)**︰請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

* **[密碼管理員 / 技術支援中心管理員](#helpdesk-administrator)**：具備此角色的使用者可以變更密碼、管理服務要求，以及監視服務健康情況。 技術支援中心管理員只能變更使用者及其他技術支援中心管理員的密碼。 

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「技術支援中心管理員」。 它是 [Azure 入口網站](https://portal.azure.com/)中的「密碼管理員」。
  >
  >
  
* **[Power BI 服務管理員](#power-bi-service-administrator)**︰具備此角色的使用者在有 Microsoft Power BI 服務時，於該服務內具有全域權限，以及管理支援票證和監控服務健康情況的能力。 如需詳細資訊，請參閱[了解 Power BI 管理員角色](https://docs.microsoft.com/en-us/power-bi/service-admin-role)。

* **[特殊權限角色管理員](#privileged-role-administrator)**：具備此角色的使用者可以管理 Azure Active Directory 中，以及 Azure AD Privileged Identity Management 內的角色指派。 此外，這個角色允許各個層面的 Privileged Identity Management 管理。

* **[報告讀取者](#reports-reader)**：具備此角色的使用者可以檢視 Office 365 系統管理中心內的使用情況報告資料和報告儀表板，以及 PowerBI 中的採用內容套件。 此外，此角色還可讓使用者存取 Azure AD 中的登入報告與活動，以及 Microsoft Graph 報告 API 所傳回的資料。 獲指派「報告讀者」角色的使用者只能存取相關的使用情況和採用計量。 他們並不具備任何系統管理權限，因此無法進行設定或存取產品特定的系統管理中心 (例如 Exchange)。 

* **[安全性系統管理員](#security-administrator)**︰具有此角色的使用者擁有安全性讀取者角色的所有唯讀權限，再加上管理與安全性相關的服務設定能力︰Azure Active Directory Identity Protection、Azure 資訊保護、Privileged Identity Management 和 Office 365 安全與規範中心。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。
  
  | 在 | 可以執行 |
  | --- | --- |
  | 身分識別防護中心 |<ul><li>「安全性讀取者」角色的所有權限。<li>此外，還能夠執行除了重設密碼以外的所有 IPC 作業。 |
  | Privileged Identity Management |<ul><li>「安全性讀取者」角色的所有權限。<li>**無法**管理 Azure AD 角色成員資格或設定。 |
  | <p>監視 Office 365 服務健康狀況</p><p>Office 365 安全性與法規遵循中心 |<ul><li>「安全性讀取者」角色的所有權限。<li>可以設定「進階威脅防護」功能中的所有設定 (惡意程式碼和病毒防護、惡意 URL 組態、URL 追蹤等)。 |
  
* **[安全性讀取者](#security-reader)**︰具有此角色的使用者具有全域唯讀存取權，包括 Azure Active Directory、身分識別保護、Privileged Identity Management，以及能夠讀取 Azure Active Directory 登入報告和稽核記錄檔的所有資訊。 角色也會在 Office 365 安全性與法規遵循中心授與唯讀權限。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

  | 在 | 可以執行 |
  | --- | --- |
  | 身分識別防護中心 |讀取安全性功能的所有安全性報告和設定資訊<ul><li>反垃圾郵件<li>加密<li>資料外洩防護<li>反惡意程式碼<li>進階威脅防護<li>防網路釣魚<li>郵件流程規則 |
  | Privileged Identity Management |<p>以唯讀方式存取 Azure AD PIM 中所顯示的一切資訊︰Azure AD 角色指派的原則和報告、安全性檢閱，以及在未來還可透過讀取來存取 Azure AD 角色指派以外案例的原則資料和報告。<p>**無法**註冊 Azure AD PIM 或對它進行任何變更。 擔任此角色的人員可以在 PIM 的入口網站中，或是透過 PowerShell，為其他角色 (例如「全域管理員」或「特殊權限角色管理員」) 的候選人啟用角色。 |
  | <p>監視 Office 365 服務健康狀況</p><p>Office 365 安全性與法規遵循中心</p> |<ul><li>讀取和管理警示<li>讀取安全性原則<li>讀取威脅情報、執行 Cloud App Discovery，以及在搜尋和調查時執行隔離<li>讀取所有報告 |

* **[服務支援系統管理員](#service-support-administrator)**︰具有此角色的使用者可以使用 Microsoft 開啟 Azure 和 Office 365 服務的支援要求，以及檢視 Azure 入口網站的服務儀表板和訊息中心以及 Office 365 系統管理入口網站。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **[SharePoint 服務管理員](#sharepoint-service-administrator)**︰具備此角色的使用者在有 Microsoft SharePoint Online 服務時，於該服務內具有全域權限，以及管理支援票證和監控服務健康情況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **[商務用 Skype / Lync 服務管理員](#lync-service-administrator)**︰在有 Microsoft 商務用 Skype 服務時，具備此角色的使用者在該服務內會具有全域權限，以及在 Azure Active Directory 中管理 Skype 特定的使用者屬性。 此外，此角色會授與管理支援票證及監控服務健全狀況的能力。 如需詳細資訊，請參閱[關於商務用 Skype 管理員角色](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)。

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Lync 服務管理員」。 它是 [Azure 入口網站](https://portal.azure.com/)中的「商務用 Skype 服務系統管理員」。
  >
  >

* **[使用者帳戶管理員](#user-account-administrator)**︰具有此角色的使用者可以建立及管理使用者和群組的所有層面。 此外，此角色包含管理支援票證及監控服務健康情況的能力。 適用某些限制。 例如，此角色並不允許刪除全域管理員。 使用者帳戶管理員只能變更使用者、技術支援中心管理員及其他使用者帳戶管理員的密碼。

| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>只能變更使用者、技術支援中心管理員及其他使用者帳戶管理員的密碼</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權，但有限制。 他無法刪除全域管理員或建立其他管理員。</p> |<p>執行 Office 產品的計費和購買作業</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>啟用或停用多重要素驗證</p><p>檢視稽核記錄檔</p> |

## <a name="deprecated-roles"></a>已被取代的角色

以下是不應使用的角色。 它們已被取代，而且未來將從 Azure AD 中移除。

* AdHoc 授權管理員
* 加入裝置
* 裝置管理員
* 裝置使用者
* 傳送電子郵件給經過驗證的使用者建立者
* 信箱管理員
* 加入工作場所裝置


### <a name="to-add-a-colleague-as-a-global-administrator"></a>將同事新增為全域管理員

1. 使用具備租用戶目錄的全域管理員或特殊權限角色管理員身分的帳戶，登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

   ![開啟 Azure AD 系統管理中心](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. 選取 [使用者]。

3. 尋找您要將其指定為全域管理員的使用者，然後開啟該使用者的刀鋒視窗。

4. 在該使用者的刀鋒視窗上，選取 [目錄角色]。
 
5. 在該目錄角色的刀鋒視窗上，選取 [全域管理員] 角色，然後儲存。

## <a name="detailed-azure-active-directory-permissions"></a>詳細 Azure Active Directory 權限
下表說明 Azure Active Directory 中賦予每個角色的特定權限。 某些角色 (例如全域管理員) 在 Azure Active Directory 以外的 Microsoft 服務中可能有額外的權限。


### <a name="application-administrator"></a>應用程式系統管理員
能夠建立及管理應用程式註冊與企業應用程式的所有層面。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Application/Create | 在 Azure Active Directory 中建立應用程式。 |
| microsoft.aad.directory/Application/Delete | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft.aad.directory/Application/Update | 更新 Azure Active Directory 中 Applications 的標準屬性。 |
| microsoft.aad.directory/Application/Update/DefaultPolicy | 更新 Azure Active Directory 中的 Applications.DefaultPolicy 屬性。 |
| microsoft.aad.directory/Application/Update/Owners | 更新 Azure Active Directory 中的 Applications.Owners 屬性。 |
| microsoft.aad.directory/AppRoleAssignment/Create | 在 Azure Active Directory 中建立 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | 刪除 Azure Active Directory 中的 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Update | 更新 Azure Active Directory 中 AppRoleAssignments 的標準屬性。 |
| microsoft.aad.directory/Policy/Create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/Policy/Delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/Policy/Update | 更新 Azure Active Directory 中原則的標準屬性。 |
| microsoft.aad.directory/Policy/Update/Owners | 更新 Azure Active Directory 中的 Policies.Owners 屬性。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | 可以代表所有使用者同意所有資源 (Azure Active Directory (Azure AD Graph 與 Microsoft Graph) 除外)。 |
| microsoft.aad.directory/ServicePrincipal/Create | 在 Azure Active Directory 中建立 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Delete | 刪除 Azure Active Directory 中的 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Update | 更新 Azure Active Directory 中 ServicePrincipals 的標準屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | 更新 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | 更新 Azure Active Directory 中的 ServicePrincipals.Owners 屬性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.reports/AllEntities/Read | 讀取 Azure AD 報告。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |

### <a name="application-developer"></a>應用程式開發人員
可建立與 [使用者可註冊應用程式] 設定不相關的應用程式註冊。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | 在 Azure Active Directory 中建立應用程式。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | 在 Azure Active Directory 中建立 AppRoleAssignments。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | 在 Azure Active Directory 中建立 OAuth2PermissionGrants。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | 在 Azure Active Directory 中建立 ServicePrincipals。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |

### <a name="billing-administrator"></a>計費管理員
能夠執行一般計費相關工作，例如更新付款資訊。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/Organization/Update | 更新 Azure Active Directory 中 Organizations 的標準屬性。 |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | 更新 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.billing/AllEntities/AllActions | 管理 Office 365 帳單的所有層面。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |

### <a name="cloud-application-administrator"></a>雲端應用程式系統管理員
能夠建立及管理應用程式註冊與企業應用程式的所有層面，但應用程式 Proxy 除外。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Application/Create | 在 Azure Active Directory 中建立應用程式。 |
| microsoft.aad.directory/Application/Delete | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft.aad.directory/Application/Update | 更新 Azure Active Directory 中 Applications 的標準屬性。 |
| microsoft.aad.directory/Application/Update/DefaultPolicy | 更新 Azure Active Directory 中的 Applications.DefaultPolicy 屬性。 |
| microsoft.aad.directory/Application/Update/Owners | 更新 Azure Active Directory 中的 Applications.Owners 屬性。 |
| microsoft.aad.directory/AppRoleAssignment/Create | 在 Azure Active Directory 中建立 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | 刪除 Azure Active Directory 中的 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Update | 更新 Azure Active Directory 中 AppRoleAssignments 的標準屬性。 |
| microsoft.aad.directory/Policy/Create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/Policy/Delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/Policy/Update | 更新 Azure Active Directory 中原則的標準屬性。 |
| microsoft.aad.directory/Policy/Update/Owners | 更新 Azure Active Directory 中的 Policies.Owners 屬性。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | 可以代表所有使用者同意所有資源 (Azure Active Directory (Azure AD Graph 與 Microsoft Graph) 除外)。 |
| microsoft.aad.directory/ServicePrincipal/Create | 在 Azure Active Directory 中建立 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Delete | 刪除 Azure Active Directory 中的 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Update | 更新 Azure Active Directory 中 ServicePrincipals 的標準屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | 更新 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | 更新 Azure Active Directory 中的 ServicePrincipals.Owners 屬性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.reports/AllEntities/Read | 讀取 Azure AD 報告。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |

### <a name="company-administrator"></a>公司系統管理員
可管理使用 Azure AD 身分識別的 Azure AD 與 Microsoft 服務的所有層面。 在 Microsoft Graph API、Azure AD Graph API 及 Azure AD PowerShell 中，是將此角色識別為「公司系統管理員」。 它是 [Azure 入口網站](https://portal.azure.com)中的「全域管理員」。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | 建立和刪除 AdministrativeUnits，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/Application/AllActions/AllProperties | 建立和刪除應用程式，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | 建立和刪除 AppRoleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | 建立和刪除 CollaborationSpaces，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/Contact/AllActions/AllProperties | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/Device/AllActions/AllProperties | 建立和刪除裝置，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | 建立和刪除 DirectoryRoles，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | 建立和刪除 DirectoryRoleTemplates，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | 建立和刪除 DirectorySettings，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | 建立和刪除 DirectorySettingTemplates，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/Domain/AllActions/AllProperties | 建立和刪除 DirectorySettingTemplates，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/Group/AllActions/AllProperties | 建立和刪除群組，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | 建立和刪除 LoginTenantBrandings，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | 建立和刪除 OAuth2PermissionGrants，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/Policy/AllActions/AllProperties | 建立和刪除原則，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | 可以代表所有使用者同意所有資源 (包含 Azure Active Directory (Azure AD Graph 與 Microsoft Graph))。 |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | 建立和刪除 ServicePrincipals，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/Organization/AllActions/AllProperties | 建立和刪除 Organizations，同時讀取及更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/User/AllActions/AllProperties | 建立和刪除使用者，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.aadconnect/AllEntities/AllActions | 建立和刪除所有資源，同時讀取及更新 microsoft.aad.aadconnect 中的標準屬性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.billing/AllEntities/AllActions | 管理 Office 365 帳單的所有層面。 |
| microsoft.aad.compliance/AllEntities/AllActions | 建立和刪除所有資源，以及讀取與更新合規性中心中的標準屬性。 |
| microsoft.aad.directorysync/AllEntities/AllActions | 執行 Azure AD Connect 中的所有動作。 |
| microsoft.aad.lockbox/AllEntities/AllActions | 管理 Lockbox 服務的所有層面。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | 管理特殊權限角色管理服務的所有層面。 |
| microsoft.aad.reports/AllEntities/AllActions | 讀取及設定 Azure AD 報告。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.crm/AllEntities/AllActions | 管理 Dynamics 365 的所有層面。 |
| microsoft.exchange/AllEntities/AllActions | 管理 Exchange Online 的所有層面。 |
| microsoft.aad.informationprotection/AllEntities/AllActions | 管理資訊保護的所有層面。 |
| microsoft.intune/AllEntities/AllActions | 管理 Intune 的所有層面。 |
| microsoft.powerbi/AllEntities/AllActions | 管理 Power BI 的所有層面。 |
| microsoft.protectioncenter/AllEntities/AllActions | 管理 Office 365 防護中心。 |
| microsoft.sharepoint/AllEntities/AllActions | 管理 SharePoint Online。 |
| microsoft.skypeforbusiness/AllEntities/AllActions | 管理商務用 Skype Online。 |

### <a name="compliance-administrator"></a>規範管理員
可讀取和管理合規性設定及 Azure AD 與 Office 365 中的報告。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.compliance/AllEntities/AllActions | 建立和刪除所有資源，以及讀取與更新合規性中心中的標準屬性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.exchange/Compliance/AllActions | 管理 Exchange Online 中的合規性。 |
| microsoft.sharepoint/Compliance/AllActions | 管理 SharePoint Online 中的合規性。 |
| microsoft.skypeforbusiness/Compliance/AllActions | 管理商務用 Skype Online 中的合規性。 |

### <a name="conditional-access-administrator"></a>條件式存取系統管理員
可管理條件式存取功能。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | 在 Azure Active Directory 中建立 ConditionalAccessPolicys。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | 刪除 Azure Active Directory 中的 ConditionalAccessPolicys。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | 讀取 Azure Active Directory 中 ConditionalAccessPolicys 的標準屬性。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | 讀取 Azure Active Directory 中的 ConditionalAccessPolicys.Owners 屬性。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | 讀取 Azure Active Directory 中的 ConditionalAccessPolicys.PolicyAppliedTo 屬性。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | 更新 Azure Active Directory 中 ConditionalAccessPolicys 的標準屬性。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | 更新 Azure Active Directory 中的 ConditionalAccessPolicys.Owners 屬性。 |

### <a name="crm-service-administrator"></a>CRM 服務管理員
可管理 Dynamics 365 產品的所有層面。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.crm/AllEntities/AllActions | 管理 Dynamics 365 的所有層面。 |

### <a name="device-administrators"></a>裝置系統管理員
此角色的成員會新增至已加入 Azure AD 的裝置上的本機系統管理員群組群組。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |

### <a name="directory-reader"></a>目錄讀取者
可讀取基本目錄資訊。 用來授與應用程式的存取權

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | 讀取 Azure Active Directory 中 AdministrativeUnits 的標準屬性。 |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | 讀取 Azure Active Directory 中的 AdministrativeUnits.Members 屬性。 |
| microsoft.aad.directory/Application/Read | 讀取 Azure Active Directory 中 Applications 的標準屬性。 |
| microsoft.aad.directory/Application/Read/Owners | 讀取 Azure Active Directory 中的 Applications.Owners 屬性。 |
| microsoft.aad.directory/CollaborationSpace/Read | 讀取 Azure Active Directory 中 CollaborationSpaces 的標準屬性。 |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | 讀取 Azure Active Directory 中的 CollaborationSpaces.Owners 屬性。 |
| microsoft.aad.directory/Contact/Read | 讀取 Azure Active Directory 中連絡人的標準屬性。 |
| microsoft.aad.directory/Contact/Read/MemberOf | 讀取 Azure Active Directory 中的 Contacts.MemberOf 屬性。 |
| microsoft.aad.directory/Device/Read | 讀取 Azure Active Directory 中裝置的標準屬性。 |
| microsoft.aad.directory/Device/Read/MemberOf | 讀取 Azure Active Directory 中的 Devices.MemberOf 屬性。 |
| microsoft.aad.directory/Device/Read/RegisteredOwners | 讀取 Azure Active Directory 中的 Devices.RegisteredOwners 屬性。 |
| microsoft.aad.directory/Device/Read/RegisteredUsers | 讀取 Azure Active Directory 中的 Devices.RegisteredUsers 屬性。 |
| microsoft.aad.directory/DirectoryRole/Read | 讀取 Azure Active Directory 中 DirectoryRoles 的標準屬性。 |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | 讀取 Azure Active Directory 中的 DirectoryRoles.EligibleMembers 屬性。 |
| microsoft.aad.directory/DirectoryRole/Read/Members | 讀取 Azure Active Directory 中的 DirectoryRoles.Members 屬性。 |
| microsoft.aad.directory/DirectorySetting/Read | 讀取 Azure Active Directory 中 DirectorySettings 的標準屬性。 |
| microsoft.aad.directory/DirectorySettingTemplate/Read | 讀取 Azure Active Directory 中 DirectorySettingTemplates 的標準屬性。 |
| microsoft.aad.directory/Domain/Read | 讀取 Azure Active Directory 中網域的標準屬性。 |
| microsoft.aad.directory/Group/Read | 讀取 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | 讀取 Azure Active Directory 中的 Groups.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/Group/Read/MemberOf | 讀取 Azure Active Directory 中的 Groups.MemberOf 屬性。 |
| microsoft.aad.directory/Group/Read/Members | 讀取 Azure Active Directory 中的 Groups.Members 屬性。 |
| microsoft.aad.directory/Group/Read/Owners | 讀取 Azure Active Directory 中的 Groups.Owners 屬性。 |
| microsoft.aad.directory/Group/Read/Settings | 讀取 Azure Active Directory 中的 Groups.Settings 屬性。 |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | 讀取 Azure Active Directory 中 OAuth2PermissionGrants 的標準屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read | 讀取 Azure Active Directory 中 ServicePrincipals 的標準屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | 讀取 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | 讀取 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | 讀取 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | 讀取 Azure Active Directory 中的 ServicePrincipals.MemberOf 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | 讀取 Azure Active Directory 中的 ServicePrincipals.OAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | 讀取 Azure Active Directory 中的 ServicePrincipals.Owners 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | 讀取 Azure Active Directory 中的 ServicePrincipals.OwnedObjects 屬性。 |
| microsoft.aad.directory/Organization/Read | 讀取 Azure Active Directory 中 Organizations 的標準屬性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/User/Read | 讀取 Azure Active Directory 中 Users 的標準屬性。 |
| microsoft.aad.directory/User/Read/AppRoleAssignments | 讀取 Azure Active Directory 中的 Users.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/User/Read/DirectReports | 讀取 Azure Active Directory 中的 Users.DirectReports 屬性。 |
| microsoft.aad.directory/User/Read/InvitedBy | 讀取 Azure Active Directory 中的 Users.InvitedBy 屬性。 |
| microsoft.aad.directory/User/Read/InvitedUsers | 讀取 Azure Active Directory 中的 Users.InvitedUsers 屬性。 |
| microsoft.aad.directory/User/Read/Manager | 讀取 Azure Active Directory 中的 Users.Manager 屬性。 |
| microsoft.aad.directory/User/Read/MemberOf | 讀取 Azure Active Directory 中的 Users.MemberOf 屬性。 |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | 讀取 Azure Active Directory 中的 Users.OAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/User/Read/OwnedDevices | 讀取 Azure Active Directory 中的 Users.OwnedDevices 屬性。 |
| microsoft.aad.directory/User/Read/OwnedObjects | 讀取 Azure Active Directory 中的 Users.OwnedObjects 屬性。 |
| microsoft.aad.directory/User/Read/RegisteredDevices | 讀取 Azure Active Directory 中的 Users.RegisteredDevices 屬性。 |

### <a name="directory-synchronization-accounts"></a>目錄同步處理帳戶
僅供 Azure AD Connect 服務使用。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/Policy/Delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/Policy/Read | 讀取 Azure Active Directory 中原則的標準屬性。 |
| microsoft.aad.directory/Policy/Read/Owners | 讀取 Azure Active Directory 中的 Policies.Owners 屬性。 |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | 讀取 Azure Active Directory 中的 Policies.PolicyAppliedTo 屬性。 |
| microsoft.aad.directory/Policy/Update | 更新 Azure Active Directory 中原則的標準屬性。 |
| microsoft.aad.directory/Policy/Update/Owners | 更新 Azure Active Directory 中的 Policies.Owners 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Create | 在 Azure Active Directory 中建立 ServicePrincipals。 |
| microsoft.aad.directory/ServicePrincipal/Read | 讀取 Azure Active Directory 中 ServicePrincipals 的標準屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | 讀取 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | 讀取 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | 讀取 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | 讀取 Azure Active Directory 中的 ServicePrincipals.MemberOf 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | 讀取 Azure Active Directory 中的 ServicePrincipals.OAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | 讀取 Azure Active Directory 中的 ServicePrincipals.Owners 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | 讀取 Azure Active Directory 中的 ServicePrincipals.OwnedObjects 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update | 更新 Azure Active Directory 中 ServicePrincipals 的標準屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignedTo 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 ServicePrincipals.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | 更新 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | 更新 Azure Active Directory 中的 ServicePrincipals.Owners 屬性。 |
| microsoft.aad.directory/Organization/Update/DirSync | 更新 Azure Active Directory 中的 Organizations.DirSync 屬性。 |
| microsoft.aad.directorysync/AllEntities/AllActions | 執行 Azure AD Connect 中的所有動作。 |

### <a name="directory-writer"></a>目錄寫入者
可讀取和寫入基本目錄資訊。 用來授與應用程式的存取權

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | 在 Azure Active Directory 中建立 DirectorySettings。 |
| microsoft.aad.directory/DirectorySetting/Delete | 刪除 Azure Active Directory 中的 DirectorySettings。 |
| microsoft.aad.directory/DirectorySetting/Update | 更新 Azure Active Directory 中 DirectorySettings 的標準屬性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中建立群組。 |
| microsoft.aad.directory/Group/CreateAsOwner | 在 Azure Active Directory 中建立群組。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/Group/Read | 讀取 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Update | 更新 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Groups.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 屬性。 |
| microsoft.aad.directory/Group/Update/Owners | 更新 Azure Active Directory 中的 Groups.Owners 屬性。 |
| microsoft.aad.directory/Group/Update/Settings | 更新 Azure Active Directory 中的 Groups.Settings 屬性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中 Users 的標準屬性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 屬性。 |

### <a name="exchange-service-administrator"></a>Exchange 服務管理員
可管理 Exchange 產品的所有層面。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.exchange/AllEntities/AllActions | 管理 Exchange Online 的所有層面。 |

### <a name="guest-inviter"></a>來賓邀請者
能夠邀請不受 [成員能夠邀請來賓] 設定限制的來賓使用者。

  > [!NOTE]
  > 此角色會繼承來賓角色的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | 邀請 Azure Active Directory 中的來賓使用者。 |
| microsoft.aad.directory/User/Read | 讀取 Azure Active Directory 中 Users 的標準屬性。 |
| microsoft.aad.directory/User/Read/AppRoleAssignments | 讀取 Azure Active Directory 中的 Users.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/User/Read/DirectReports | 讀取 Azure Active Directory 中的 Users.DirectReports 屬性。 |
| microsoft.aad.directory/User/Read/InvitedBy | 讀取 Azure Active Directory 中的 Users.InvitedBy 屬性。 |
| microsoft.aad.directory/User/Read/InvitedUsers | 讀取 Azure Active Directory 中的 Users.InvitedUsers 屬性。 |
| microsoft.aad.directory/User/Read/Manager | 讀取 Azure Active Directory 中的 Users.Manager 屬性。 |
| microsoft.aad.directory/User/Read/MemberOf | 讀取 Azure Active Directory 中的 Users.MemberOf 屬性。 |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | 讀取 Azure Active Directory 中的 Users.OAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/User/Read/OwnedDevices | 讀取 Azure Active Directory 中的 Users.OwnedDevices 屬性。 |
| microsoft.aad.directory/User/Read/OwnedObjects | 讀取 Azure Active Directory 中的 Users.OwnedObjects 屬性。 |
| microsoft.aad.directory/User/Read/RegisteredDevices | 讀取 Azure Active Directory 中的 Users.RegisteredDevices 屬性。 |

### <a name="helpdesk-administrator"></a>服務台系統管理員
能夠為非系統管理員與技術服務人員系統管理員重設密碼。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | 更新 Azure Active Directory 中有限的管理員與其他技術服務人員管理員的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |

### <a name="information-protection-administrator"></a>資訊保護管理員
可管理 Azure 資訊保護產品的所有層面。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Group/Read | 讀取 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.informationprotection/AllEntities/AllActions | 管理資訊保護的所有層面。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |

### <a name="intune-service-administrator"></a>Intune 服務管理員
可管理 Intune 產品的所有層面。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | 在 Azure Active Directory 中建立合約。 |
| microsoft.aad.directory/Contact/Delete | 刪除 Azure Active Directory 中的合約。 |
| microsoft.aad.directory/Contact/Update | 更新 Azure Active Directory 中連絡人的標準屬性。 |
| microsoft.aad.directory/Device/Create | 在 Azure Active Directory 中建立裝置。 |
| microsoft.aad.directory/Device/Delete | 刪除 Azure Active Directory 中的裝置。 |
| microsoft.aad.directory/Device/Update | 更新 Azure Active Directory 中裝置的標準屬性。 |
| microsoft.aad.directory/Device/Update/RegisteredOwners | 更新 Azure Active Directory 中的 Devices.RegisteredOwners 屬性。 |
| microsoft.aad.directory/Device/Update/RegisteredUsers | 更新 Azure Active Directory 中的 Devices.RegisteredUsers 屬性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中建立群組。 |
| microsoft.aad.directory/Group/CreateAsOwner | 在 Azure Active Directory 中建立群組。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/Group/Delete | 刪除 Azure Active Directory 中的群組。 |
| microsoft.aad.directory/Group/Read | 讀取 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Read/HiddenMembers | 讀取 Azure Active Directory 中的 Groups.HiddenMembers 屬性。 |
| microsoft.aad.directory/Group/Restore | 還原 Azure Active Directory 中的群組。 |
| microsoft.aad.directory/Group/Update | 更新 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Groups.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 屬性。 |
| microsoft.aad.directory/Group/Update/Owners | 更新 Azure Active Directory 中的 Groups.Owners 屬性。 |
| microsoft.aad.directory/Group/Update/Settings | 更新 Azure Active Directory 中的 Groups.Settings 屬性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中 Users 的標準屬性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 屬性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.intune/AllEntities/AllActions | 管理 Intune 的所有層面。 |

### <a name="lync-service-administrator"></a>Lync 服務管理員
可管理商務用 Skype 產品的所有層面。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.skypeforbusiness/AllEntities/AllActions | 管理商務用 Skype Online。 |

### <a name="message-center-reader"></a>訊息中心讀取者
只可在 Office 365 訊息中心讀取及更新其組織的訊息。 

  > [!NOTE]
  > 此角色會繼承目錄讀取者角色的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Group/Read | 讀取 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | 建立和刪除所有資源，以及讀取與更新訊息中心的標準屬性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |

### <a name="partner-tier1-support"></a>合作夥伴第 1 層支援
請勿使用 - 不適用於一般用途。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.directory/Contact/Create | 在 Azure Active Directory 中建立合約。 |
| microsoft.aad.directory/Contact/Delete | 刪除 Azure Active Directory 中的合約。 |
| microsoft.aad.directory/Contact/Update | 更新 Azure Active Directory 中連絡人的標準屬性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中建立群組。 |
| microsoft.aad.directory/Group/CreateAsOwner | 在 Azure Active Directory 中建立群組。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/Group/Read | 讀取 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 屬性。 |
| microsoft.aad.directory/Group/Update/Owners | 更新 Azure Active Directory 中的 Groups.Owners 屬性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/User/Delete | 刪除 Azure Active Directory 中的使用者。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/User/Restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中 Users 的標準屬性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 屬性。 |
| microsoft.aad.directory/User/Update/PasswordUserScope | 在 Azure Active Directory 中更新非管理員的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |

### <a name="partner-tier2-support"></a>合作夥伴第 2 層支援
請勿使用 - 不適用於一般用途。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.directory/Contact/Create | 在 Azure Active Directory 中建立合約。 |
| microsoft.aad.directory/Contact/Delete | 刪除 Azure Active Directory 中的合約。 |
| microsoft.aad.directory/Contact/Update | 更新 Azure Active Directory 中連絡人的標準屬性。 |
| microsoft.aad.directory/Domain/AllActions | 建立和刪除網域，以及在 Azure Active Directory 中讀取和更新標準屬性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中建立群組。 |
| microsoft.aad.directory/Group/Delete | 刪除 Azure Active Directory 中的群組。 |
| microsoft.aad.directory/Group/Read | 讀取 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Restore | 還原 Azure Active Directory 中的群組。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 屬性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/Organization/Update | 更新 Azure Active Directory 中 Organizations 的標準屬性。 |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | 更新 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/User/Delete | 刪除 Azure Active Directory 中的使用者。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/User/Restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中 Users 的標準屬性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 屬性。 |
| microsoft.aad.directory/User/Update/Password | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |

### <a name="power-bi-service-administrator"></a>Power BI 服務管理員
可管理 Power BI 產品的所有層面。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.powerbi/AllEntities/AllActions | 管理 Power BI 的所有層面。 |

### <a name="privileged-role-administrator"></a>特殊權限角色管理員
可管理 Azure AD 中的角色指派

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | 更新 Azure Active Directory 中 DirectoryRoles 的標準屬性。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | 管理特殊權限角色管理服務的所有層面。 |

### <a name="security-administrator"></a>安全性系統管理員
可讀取安全性資訊和報告

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | 更新 Azure Active Directory 中的 Applications.DefaultPolicy 屬性。 |
| microsoft.aad.directory/Policy/Create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/Policy/Delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/Policy/Update | 更新 Azure Active Directory 中原則的標準屬性。 |
| microsoft.aad.directory/Policy/Update/Owners | 更新 Azure Active Directory 中的 Policies.Owners 屬性。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | 更新 Azure Active Directory 中的 ServicePrincipals.DefaultPolicy 屬性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | 讀取 Privileged Identity Management 的所有層面。 |
| microsoft.protectioncenter/AllEntities/Read | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.protectioncenter/AllEntities/Update | 管理 Office 365 防護中心。 |

### <a name="reports-reader"></a>報告讀者
可讀取登入與稽核報告。

  > [!NOTE]
  > 此角色會繼承目錄讀取者角色的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | 讀取 Azure AD 報告。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.usagereports/AllEntities/Read | 讀取 Office 365 使用量報告。 |

### <a name="security-reader"></a>安全性讀取者
可讀取安全性資訊及 Azure AD 與 Office 365 中的報告。

  > [!NOTE]
  > 此角色會繼承目錄讀取者角色的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.privilegedidentitymanagement/AllEntities/Read | 讀取 Privileged Identity Management 的所有層面。 |
| microsoft.protectioncenter/AllEntities/Read | 讀取 Office 365 防護中心的所有層面。 |

### <a name="service-support-administrator"></a>服務支援管理員
可讀取服務健康情況資訊及管理支援票證。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |

### <a name="sharepoint-service-administrator"></a>SharePoint 服務管理員
可管理 SharePoint 服務的所有層面。

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.sharepoint/AllEntities/AllActions | 管理 SharePoint Online。 |

### <a name="user-account-administrator"></a>使用者帳戶管理員
可管理使用者和群組的所有層面

  > [!NOTE]
  > 此角色會繼承[使用者角色](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions)的其他權限。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | 在 Azure Active Directory 中建立 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | 刪除 Azure Active Directory 中的 AppRoleAssignments。 |
| microsoft.aad.directory/AppRoleAssignment/Update | 更新 Azure Active Directory 中 AppRoleAssignments 的標準屬性。 |
| microsoft.aad.directory/Contact/Create | 在 Azure Active Directory 中建立合約。 |
| microsoft.aad.directory/Contact/Delete | 刪除 Azure Active Directory 中的合約。 |
| microsoft.aad.directory/Contact/Update | 更新 Azure Active Directory 中連絡人的標準屬性。 |
| microsoft.aad.directory/Group/Create | 在 Azure Active Directory 中建立群組。 |
| microsoft.aad.directory/Group/CreateAsOwner | 在 Azure Active Directory 中建立群組。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/Group/Delete | 刪除 Azure Active Directory 中的群組。 |
| microsoft.aad.directory/Group/Read | 讀取 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Read/HiddenMembers | 讀取 Azure Active Directory 中的 Groups.HiddenMembers 屬性。 |
| microsoft.aad.directory/Group/Restore | 還原 Azure Active Directory 中的群組。 |
| microsoft.aad.directory/Group/Update | 更新 Azure Active Directory 中 Groups 的標準屬性。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Groups.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/Group/Update/Members | 更新 Azure Active Directory 中的 Groups.Members 屬性。 |
| microsoft.aad.directory/Group/Update/Owners | 更新 Azure Active Directory 中的 Groups.Owners 屬性。 |
| microsoft.aad.directory/Group/Update/Settings | 更新 Azure Active Directory 中的 Groups.Settings 屬性。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | 讀取 Azure Active Directory 中的 Organizations.TrustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/User/AssignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/User/Create | 在 Azure Active Directory 中建立使用者。 |
| microsoft.aad.directory/User/Delete | 刪除 Azure Active Directory 中的使用者。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/User/Restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.aad.directory/User/Update | 更新 Azure Active Directory 中 Users 的標準屬性。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | 更新 Azure Active Directory 中的 Users.AppRoleAssignments 屬性。 |
| microsoft.aad.directory/User/Update/Manager | 更新 Azure Active Directory 中的 Users.Manager 屬性。 |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | 更新 Azure Active Directory 中有限的管理員、技術服務管理員與其他使用者帳戶管理員的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.aad.accessservice/AllEntities/AllActions | 建立和刪除所有資源，以及在 Azure 存取控制中讀取和更新標準屬性。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | 建立和管理 Office 365 支援票證。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | 讀取及設定 Office 365 服務健康情況。 |

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何變更 Azure 訂用帳戶的系統管理員，請參閱 [如何新增或變更 Azure 系統管理員角色](../billing-add-change-azure-subscription-administrator.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](../role-based-access-control/rbac-and-directory-admin-roles.md)
* 如需有關 Azure Active Directory 與您的 Azure 訂用帳戶產生關聯之方式的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](active-directory-how-subscriptions-associated-directory.md)