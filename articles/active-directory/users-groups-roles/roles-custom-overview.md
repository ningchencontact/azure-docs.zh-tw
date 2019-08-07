---
title: 預覽具有可自訂許可權的 Azure 系統管理員角色-Azure Active Directory |Microsoft Docs
description: 預覽用於委派身分識別管理的自訂 Azure AD 角色。 管理 Azure 入口網站、PowerShell 或圖形 API 中的 Azure 角色。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82638e3e102f7b8e39cd797960a11f3193132bc1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779380"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory 中的自訂系統管理員角色 (預覽)

本文說明如何在 Azure Active Directory (Azure AD) 中瞭解新的自訂 RBAC (角色型存取控制) 和資源範圍。 自訂 RBAC 角色會顯示[內建角色](directory-assign-admin-roles.md)的基礎許可權, 讓您可以建立和組織自己的自訂角色。 資源範圍可讓您指派自訂角色來管理某些資源 (例如一個應用程式), 而不需提供所有資源 (所有應用程式) 的存取權。

使用自訂 RBAC 角色授與許可權是兩個步驟的程式。 首先, 您會建立自訂角色定義, 並從預設清單新增其許可權。 這些是內建角色中所使用的相同許可權。 建立角色之後, 您可以藉由建立角色指派將它指派給某人。 這個兩個步驟的程式可讓您建立一個角色, 並在不同的範圍內多次指派。 自訂角色可以在目錄範圍指派, 也可以在物件範圍指派。 物件範圍的範例就是單一應用程式。 如此一來, 就可以將相同的角色指派給目錄中的所有應用程式 Sally, 然後只 Naveen Contoso Expense Reports 應用程式。

第一版的自訂 RBAC 角色包含建立角色以指派管理應用程式註冊許可權的能力。 經過一段時間, 將會新增組織資源 (例如企業應用程式、使用者和裝置) 的其他許可權。

預覽功能:

- 用於建立和管理自訂角色, 並將其指派給整個組織範圍內使用者的入口網站 UI 更新
- 具有新 Cmdlet 的預覽 PowerShell 模組可執行下列動作:
  - 建立和管理自訂角色
  - 使用整個組織或每個應用程式註冊範圍來指派自訂角色
  - 在整個組織範圍指派內建角色 (與 GA Cmdlet 的同位)
  - Azure AD 圖形 API 支援

Azure AD 以角色為基礎的存取控制是 Azure AD 的公開預覽功能, 適用于任何付費 Azure AD 授權方案。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="understand-azure-ad-role-based-access-control"></a>瞭解 Azure AD 以角色為基礎的存取控制

Azure AD 以角色為基礎的存取控制可讓您將自訂的角色指派給只允許單一類型 Azure AD 資源允許的動作。 Azure AD 以角色為基礎的存取會在類似于 Azure 角色型存取控制的概念上運作 ([AZURE rbac](../../role-based-access-control/overview.md)用於 azure 資源存取, 但 Azure AD 角色型存取控制是以 Microsoft Graph 為基礎, 而 Azure RBAC 是以 Azure Resource Manager 為基礎。 不過, 這兩個系統都會以角色指派的功能為基礎。

### <a name="role-assignments"></a>角色指派

使用 Azure AD 以角色為基礎的存取控制來控制存取的方式, 是建立**角色指派**, 用來強制執行許可權。 角色指派是由三個元素所組成:

- 安全性主體
- 角色定義
- 資源範圍

建立角色指派可授與存取權，而移除角色指派則可撤銷存取權。 您可以使用 Azure 入口網站、Azure AD PowerShell 和圖形 API 來[建立角色指派](roles-create-custom.md)。 您可以另外[查看自訂角色的指派](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)。

下圖顯示角色指派的範例。 在此範例中, 已將 SalesForce 應用程式範圍中的[應用程式系統管理員](directory-assign-admin-roles.md#application-administrator)角色指派給 Chris 環保。 Chris 沒有管理任何其他應用程式的存取權, 除非它們是不同角色指派的一部分。

![角色指派是如何強制執行許可權, 而且有三個部分](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>安全性主體

安全性主體代表要獲指派 Azure AD 資源存取權的使用者或服務主體。 *使用者*是 Azure Active Directory 中具有使用者設定檔的個人。 *服務主體*是應用程式或服務用來存取特定 Azure AD 資源的安全性身分識別。

安全性主體與使用者身分識別相似之處在于, 它代表使用者名稱和密碼或憑證, 但是對於應用程式或服務, 而不是使用者。

### <a name="role"></a>Role

角色定義 (或角色) 是許可權的集合。 角色定義會列出可在 Azure AD 資源上執行的作業, 例如建立、讀取、更新和刪除。 Azure AD 中有兩種角色類型:

- 由 Microsoft 建立且無法變更的內建角色。 全域管理員內建角色具有所有 Azure AD 資源的擁有權限。
- 您的組織所建立及管理的自訂角色。

### <a name="scope"></a>`Scope`

範圍是允許的動作對特定 Azure AD 資源的限制。 當您指派角色時, 您可以指定範圍, 將系統管理員允許的動作限制為特定的資源。 例如, 如果您想要授與開發人員自訂角色, 但只是為了管理特定的應用程式註冊, 您可以在角色指派中包含特定的應用程式註冊作為範圍。

  > [!Note]
  > 您可以在目錄範圍和資源範圍中指派自訂角色。 這些使用者尚未在系統管理單位範圍中指派。
  > 可以在目錄範圍中指派內建角色, 而在某些情況下, 可以在系統管理單位範圍中指派。 它們尚未在物件範圍中指派。

## <a name="required-license-plan"></a>必要的授權方案

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 使用[Azure 入口網站、Azure AD PowerShell 和圖形 API](roles-create-custom.md)來建立自訂角色指派
- [查看自訂角色的指派](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
