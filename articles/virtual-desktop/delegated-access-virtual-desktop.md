---
title: 委派的存取權，預覽版 Windows 虛擬桌面-Azure
description: 如何委派系統管理功能，針對 Windows 虛擬桌面預覽的部署，包括範例。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 250aea52de63a6397ce00e9cadcadf3a8ba39858
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58399989"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>在 Windows 虛擬桌面預覽中的委派的存取

Windows 虛擬桌面預覽具有委派的存取模型，可讓您定義特定的使用者允許將角色指派的存取權數量。 角色指派有三個元件： 安全性主體、 角色定義和範圍。 Windows 虛擬桌面的委派的存取模型根據 Azure RBAC 模型。 若要深入了解特定的角色指派和其元件，請參閱[Azure 角色型存取控制概觀](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)。

Windows 虛擬桌面委派存取支援每個項目的角色指派下列值：

* 安全性主體
    * 使用者
    * 服務主體
* 角色定義
    * 內建角色
* 影響範圍
    * 租用戶群組
    * 租用戶
    * 主應用程式集區
    * 應用程式群組

## <a name="built-in-roles"></a>內建角色

在 Windows 虛擬桌面的委派的存取具有數個內建角色定義，您可以指派給使用者和服務主體。

* RDS 擁有者可以管理所有項目，包括資源的存取權。
* RDS 參與者可以管理資源的存取權以外的所有內容。
* RDS 讀取器可以檢視所有項目，但無法進行任何變更。
* RDS 操作員可以檢視診斷的活動。

## <a name="powershell-cmdlets-for-role-assignments"></a>角色指派的 PowerShell cmdlet

您可以執行下列 cmdlet 來建立、 檢視和編輯角色指派：

* **取得 RdsRoleAssignment**會顯示一份角色指派。
* **新 RdsRoleAssignment**會建立新的角色指派。
* **設定 RdsRoleAssignment**編輯角色指派。

### <a name="accepted-parameters"></a>接受的參數

您可以修改基本的三個 cmdlet，使用下列參數：

* **AadTenantId**： 指定的服務主體所屬的 Azure Active Directory 租用戶識別碼。
* **AppGroupName**： 遠端桌面應用程式群組的名稱。
* **診斷**： 指出診斷範圍。 (必須與其中一個配對**基礎結構**或是**租用戶**參數。)
* **HostPoolName**： 遠端桌面的主應用程式集區的名稱。
* **基礎結構**： 表示的基礎結構範圍。
* **RoleDefinitionName**： 指派給使用者、 群組或應用程式的遠端桌面服務角色型存取控制角色的名稱。 （例如，遠端桌面服務擁有者、 遠端桌面服務的讀取器等等。）
* **ServerPrincipleName**: Azure Active Directory 應用程式的名稱。
* **SignInName**： 使用者的電子郵件地址或使用者主體名稱。
* **TenantName**： 遠端桌面的租用戶的名稱。

## <a name="next-steps"></a>後續步驟

每個角色可以使用 PowerShell cmdlet 的更完整清單，請參閱 < [PowerShell 參考](/powershell/windows-virtual-desktop/overview)。

如需如何設定 Windows 虛擬桌面環境的指導方針，請參閱[Windows 虛擬桌面預覽環境](environment-setup.md)。
