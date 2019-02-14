---
title: 委派應用程式系統管理員角色 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中授與權限的應用程式存取管理委派角色
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316ebb7774a0420087bf9ed5c099a91d78a96fb0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56181213"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中指派應用程式系統管理員角色

 Azure AD 可讓您將應用程式存取的管理委派給一組內建的系統管理角色。 委派特殊權限來管理應用程式存取工作除了可減輕全域管理員的工作負荷，也可改善安全性狀態並降低未經授權存取的可能性。 有關於委派問題和一般指導方針的討論，請見[在 Azure Active Directory 中委派管理](roles-concept-delegation.md)。

## <a name="delegate-app-administration"></a>委派應用程式系統管理

下列角色會授與相關權限，用以管理應用程式註冊、單一登入設定、使用者和群組指派，以及同意委派的權限和應用程式權限 (不包括 Microsoft Graph 和 Azure AD Graph)。 唯一的差別在於，應用程式系統管理員角色還會授與管理應用程式 Proxy 設定的權限。 而這些角色都不會授與管理條件式存取設定的能力。
> [!IMPORTANT]
> 獲指派此角色的使用者可以將認證新增至應用程式，並使用這些認證來模擬應用程式的身分識別。 這種對應用程式身分識別的模擬，可以提高使用者依據其 Azure AD 中的角色指派執行作業的權限。 獲指派此角色的使用者可在模擬應用程式時建立或更新使用者或其他物件。

若要授與對 Azure 入口網站中的應用程式存取進行管理的能力：

1. 使用租用戶中的全域管理員角色適用的帳戶，登入 [Azure AD 租用戶](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 如果您有足夠的權限，請開啟[角色和系統管理員頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)。
3. 開啟下列其中一個角色以查看其成員指派：
  * **應用程式系統管理員**
  * **雲端應用程式系統管理員**
4. 在角色的 [成員] 頁面上，選取 [新增成員]。
5. 選取一或多個要新增至角色的成員。 <!--Members can be users or groups.-->

您可以在[可用的角色](directory-assign-admin-roles.md#available-roles)中檢視這些角色的描述。

## <a name="delegate-app-registration"></a>委派應用程式註冊

根據預設，所有使用者皆可建立應用程式註冊，但您可以選擇性地授與建立應用程式註冊的權限，或是同意進行應用程式授權的權限。

1. 使用租用戶中的全域管理員角色適用的帳戶，登入 [Azure AD 租用戶](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 如果您已取得足夠的權限，請進行下列一或兩項設定：
  * 在[租用戶的使用者設定頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)上，將 [使用者可以註冊應用程式] 設為 [否]。
  * 在[企業應用程式的使用者設定](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)上，將 [使用者可同意應用程式代表自己存取公司資料] 設為 [否]。
3. 然後，視需要將需要此權限的使用者指派為應用程式開發人員角色的成員。

當使用者註冊應用程式時，系統會自動將他們新增為應用程式的第一個擁有者。

## <a name="delegate-app-ownership"></a>委派應用程式擁有權

應用程式擁有者和應用程式註冊擁有者分別只能管理他們所擁有的應用程式或應用程式註冊。 例如，當您新增 Salesforce 應用程式的擁有者時，而該擁有者將可管理 Salesforce 的存取和設定，但無法管理任何其他應用程式。 一個應用程式可以有許多擁有者，而一個使用者也可以是許多應用程式的擁有者。

應用程式擁有者可以：

* 變更應用程式屬性，例如應用程式要求的名稱和權限
* 管理認證
* 設定單一登入
* 指派使用者存取權
* 新增或移除其他擁有者
* 編輯應用程式資訊清單
* 將應用程式發佈至應用程式資源庫

> [!IMPORTANT]
> 獲指派此角色的使用者可以將認證新增至應用程式，並使用這些認證來模擬應用程式的身分識別。 這種對應用程式身分識別的模擬，可以提高使用者依據其 Azure AD 中的角色指派執行作業的權限。 獲指派此角色的使用者可在模擬應用程式時建立或更新使用者或其他物件。

應用程式註冊的擁有者可檢視和編輯應用程式註冊。

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>將擁有者指派給應用程式

1. 使用租用戶的應用程式系統管理員或雲端應用程式系統管理員適用的帳戶，登入 [Azure AD 租用戶](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 在租用戶的[應用程式註冊頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)上選取應用程式，以開啟應用程式的 [概觀] 頁面。
3. 選取 [擁有者] 以查看應用程式的擁有者清單。
4. 選取 [新增]，以選取一或多個要新增至應用程式的擁有者。

### <a name="to-assign-an-owner-to-an-application-registration"></a>將擁有者指派給應用程式註冊

1. 使用租用戶中的應用程式系統管理員或雲端應用程式系統管理員角色適用的帳戶，登入 [Azure AD 租用戶](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 如果您有足夠的權限，請在租用戶的[企業應用程式頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)上選取應用程式註冊，加以開啟。
3. 選取 [Settings] \(設定) 。
4. 選取 [設定] 頁面上的 [擁有者]，以查看應用程式的擁有者清單。
5. 選取 [新增擁有者]，以選取一或多個要新增至應用程式的擁有者。

## <a name="next-steps"></a>後續步驟

* [Azure AD 系統管理員角色參考](directory-assign-admin-roles.md)
