---
title: 設定自助群組管理 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中建立及管理安全性群組或 Office 365 群組，並要求安全性群組或 Office 365 群組成員資格
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5122c9142eccf12193e7e429a3af5ac44fbb8fd6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111322"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>設定 Azure Active Directory 中的自助式群組管理 

您可以讓使用者建立和管理他們自己的安全性群組或 Azure Active Directory (Azure AD) 中的 Office 365 群組。 群組的擁有者可以核准或拒絕成員資格要求，並可以委派控制權的群組成員資格。 自助式群組管理功能不適用於擁有郵件功能的安全性群組或通訊群組清單。 

## <a name="self-service-group-membership-defaults"></a>自助服務群組成員資格預設值

當在 Azure 入口網站中建立安全性群組，或使用 Azure AD PowerShell，只有該群組的擁有者可以更新成員資格。 在中建立的安全性群組[存取面板](https://account.activedirectory.windowsazure.com/r#/joinGroups)和 Office 365 的所有群組都都可以加入所有的使用者是否擁有者核准，或自動核准。 在存取面板中，您可以變更成員資格選項，當您建立群組。

在中建立群組 | 安全性群組預設行為 | Office 365 群組的預設行為
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | 只有擁有者可以新增成員<br>但無法使用，若要在存取面板中顯示 | 開啟以供所有使用者加入
[Azure 入口網站](https://portal.azure.com) | 只有擁有者可以新增成員<br>但無法使用，若要在存取面板中顯示<br>擁有者未在群組建立時自動指派 | 開啟以供所有使用者加入
[存取面板](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 開啟以供所有使用者加入<br>建立群組時，就可以變更成員資格選項 | 開啟以供所有使用者加入<br>建立群組時，就可以變更成員資格選項

## <a name="self-service-group-management-scenarios"></a>自助式群組管理案例

* **委派的群組管理** ：例如，管理對系統管理員公司所使用之 SaaS 應用程式存取權的系統管理員。 管理這些存取權限會變得很麻煩，所以此系統管理員會要求企業業主建立新的群組。 系統管理員會將應用程式的存取權指派給新群組，並新增至所有人員已存取該應用程式的群組。 接著，企業業主可以新增更多使用者，而且這些使用者會自動佈建到應用程式。 企業業主不需要等待系統管理員管理使用者的存取權。 如果系統管理員授與相同的權限的管理員在不同業務群組中，則該人員也可以管理他們自己的群組成員的存取。 企業業主或管理員都不可以檢視或管理彼此的群組成員資格。 系統管理員仍然可以看到可以存取應用程式的所有使用者，並視需要封鎖存取權。
* **自助式群組管理** ：此案例的其中一個範例是都已獨立設定 SharePoint Online 網站的兩個使用者。 他們想要賦與彼此團隊各自網站的存取權。 為了達成此目的，他們可以在 Azure AD 中建立一個群組，並各自在 SharePoint Online 中選取該群組來提供對他們網站的存取權。 當有人想要存取權時，他們會從 [存取面板] 中要求，而且在核准之後，他們就可以自動取得這兩個 SharePoint Online 網站的存取權。 之後，其中一個人會決定存取網站的所有人員也可以存取特定的 SaaS 應用程式。 SaaS 應用程式的系統管理員可以將此應用程式的存取權限新增到 SharePoint Online 網站。 從那時起，任何獲得核准的要求都可以存取兩個 SharePoint Online 網站，也可以存取此 SaaS 應用程式。

## <a name="make-a-group-available-for-user-self-service"></a>提供可供使用者自助服務的群組
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組]，然後選取 [群組設定]。
3. 將 [啟用自助式群組管理] 設定為 [是]。
4. 將 [使用者可以建立安全性群組] 或 [使用者可以建立 Office 365 群組] 設定為 [是]。
   * 若已啟用這些設定，目錄中的所有使用者都可以建立新的安全性群組，並將成員新增至這些群組。 這些新的群組也會顯示在其他所有使用者的 [存取面板] 中。 如果群組的原則設定允許，其他使用者可以建立加入這些群組的要求。 
   * 若已停用這些設定，使用者就無法建立群組，也無法變更其擁有的現有群組。 不過，他們仍然可以管理這些群組的成員資格，以及核准其他使用者加入其群組的要求。

您也可以使用 [可管理安全性群組的使用者]  和 [可管理 Office 365 群組的使用者]，對您使用者的自助式群組管理功能實現更精細的存取控制。 當 [使用者可以建立群組]  啟用時，租用戶中的所有使用者都可以建立新群組，並將成員新增至這些群組。 將他們設定為 [部分]，表示您要將群組管理限制為只有一組有限的使用者。 當這個參數設定為 [部分] 時，您必須先將使用者新增至 SSGMSecurityGroupsUsers 群組，他們才能建立新的群組並在其中新增成員。 將 [可為安全性群組使用自助服務的使用者]  和 [可管理 Office 365 群組的使用者] 設定為 [全部]，表示您允許租用戶中的所有使用者建立新群組。

您也可以使用 [可管理安全性群組的群組]  或 [可管理 Office 365 群組的群組]，指定其成員可使用自助服務的單一群組。

## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](../fundamentals/active-directory-manage-groups.md)
* [設定群組設定的 Azure Active Directory Cmdlet](groups-settings-cmdlets.md)
* [Azure Active Directory 中的應用程式管理](../manage-apps/what-is-application-management.md)
* [什麼是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
