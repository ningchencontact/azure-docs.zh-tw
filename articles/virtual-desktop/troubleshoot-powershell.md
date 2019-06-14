---
title: Windows 虛擬桌面 PowerShell-Azure
description: 如何設定 Windows 虛擬桌面的租用戶環境時，針對使用 PowerShell 的問題進行疑難排解。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927503"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows 虛擬桌面 PowerShell

您可以使用本文來解決使用 PowerShell 與 Windows 虛擬桌面時的錯誤和問題。 如需有關遠端桌面服務的 PowerShell 的詳細資訊，請參閱[Windows 虛擬桌面 Powershell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/)。

## <a name="provide-feedback"></a>提供意見反應

我們目前不採用 Windows 虛擬桌面處於預覽狀態時的支援案例。 請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 虛擬桌面設定期間使用的 PowerShell 命令

此區段會列出 PowerShell 命令，通常用於設定 Windows 虛擬桌面時，並提供解決在使用它們時可能發生之問題的方法。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Error:新增 RdsAppGroupUser 命令-指定的 UserPrincipalName 已指派給指定的主應用程式集區中的 RemoteApp 應用程式群組

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**原因：** 用的使用者名稱具有已指派至不同類型的應用程式群組。 使用者無法指派給相同的工作階段主應用程式集區下的這兩種遠端桌面和遠端應用程式群組。

**修正：** 如果使用者需要遠端應用程式和遠端桌面，建立不同的主應用程式集區，或授與使用者存取將會允許任何應用程式使用工作階段主機 VM 的遠端桌面。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Error:新增 RdsAppGroupUser 命令-指定的 UserPrincipalName 並不存在於與遠端桌面的租用戶相關聯的 Azure Active Directory

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**原因：** 繫結至 Windows 虛擬桌面的租用戶的 Azure Active Directory 中找不到指定之使用者的-UserPrincipalName。

**修正：** 請確認下列清單中的項目。

- 使用者已同步處理至 Azure Active Directory。
- 使用者不受限於企業對消費者 (B2C) 或企業對企業 (B2B) 的商務。
- Windows 虛擬桌面的租用戶會連結到正確的 Azure Active Directory。

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Error:Get-RdsDiagnosticActivities-若要查詢的管理服務使用者未獲授權

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**原因：** -TenantName 參數

**修正：** 發出 Get-RdsDiagnosticActivities 與-TenantName <TenantName>。

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Error:Get-RdsDiagnosticActivities-若要查詢的管理服務的使用者未獲授權

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**原因：** 使用-部署參數。

**修正：** -部署參數僅供部署系統管理員。 這些系統管理員通常是遠端桌面服務/Windows 虛擬桌面小組的成員。 取代為部署參數與-TenantName <TenantName>。

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Error:新 RdsRoleAssignment-若要查詢的管理服務的使用者未獲授權

**原因 1：** 租用戶上，所使用的帳戶不具有遠端桌面服務擁有者權限。

**1 的修正：** 若要執行的角色指派，必須具有遠端桌面服務擁有者權限的使用者。

**原因 2：** 所使用的帳戶具有遠端桌面服務的擁有者權限，但不是租用戶的 Azure Active Directory 的一部分或查詢使用者所在的 Azure Active Directory 的權限。

**2 的修正：** 若要執行的角色指派，必須具有 Active Directory 權限的使用者。

>[!Note]
>新 RdsRoleAssignment 無法授與權限給沒有在 Azure Active Directory (AD) 使用者。

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 的虛擬桌面和擴大追蹤的概觀，請參閱 <<c0> [ 疑難排解概觀、 意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對問題進行疑難排解在 Windows 虛擬桌面環境中建立的租用戶和主應用程式集區時，請參閱[租用戶，然後主應用程式集區建立](troubleshoot-set-up-issues.md)。
- 若要設定虛擬機器 (VM) 中 Windows 虛擬桌面時疑難排解問題，請參閱[工作階段主機的虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要疑難排解使用 Windows 的虛擬桌面用戶端連線的問題，請參閱[遠端桌面用戶端連線](troubleshoot-client-connection.md)。
- 若要深入了解預覽服務，請參閱[Windows Desktop 預覽環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。