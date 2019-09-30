---
title: Windows 虛擬桌面 PowerShell-Azure
description: 當您設定 Windows 虛擬桌面租使用者環境時, 如何針對 PowerShell 問題進行疑難排解。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 021560f9538d2a95492ee04467e8733caa226eec
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679413"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows 虛擬桌面 PowerShell

使用這篇文章來解決搭配 Windows 虛擬桌面使用 PowerShell 時的錯誤和問題。 如需遠端桌面服務 PowerShell 的詳細資訊, 請參閱[Windows 虛擬桌面 powershell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/)。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 虛擬桌面安裝期間使用的 PowerShell 命令

本節列出在設定 Windows 虛擬桌面時通常會使用的 PowerShell 命令, 並提供方法來解決使用它們時可能發生的問題。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Error:RdsAppGroupUser 命令--指定的 UserPrincipalName 已指派給指定主機集區中的 RemoteApp 應用程式群組

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**原因：** 使用的使用者名稱已指派給不同類型的應用程式群組。 無法將使用者指派給相同工作階段主機集區下的遠端桌面和遠端應用程式群組。

**補丁**如果使用者同時需要遠端應用程式和遠端桌面, 請建立不同的主機集區, 或授與使用者遠端桌面的存取權, 這將允許在工作階段主機 VM 上使用任何應用程式。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Error:RdsAppGroupUser 命令--指定的 UserPrincipalName 不存在於與遠端桌面租使用者相關聯的 Azure Active Directory 中

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**原因：** 在系結至 Windows 虛擬桌面租使用者的 Azure Active Directory 中找不到-UserPrincipalName 所指定的使用者。

**補丁**確認下列清單中的專案。

- 使用者已同步至 Azure Active Directory。
- 使用者不會系結至企業對消費者 (B2C) 或企業對企業 (B2B) 商務。
- Windows 虛擬桌面租使用者系結至正確的 Azure Active Directory。

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Error:RdsDiagnosticActivities--使用者未獲授權查詢管理服務

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**原因:** -TenantName 參數

**補丁**發出 RdsDiagnosticActivities with-TenantName \<TenantName >。

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Error:RdsDiagnosticActivities--使用者未獲授權查詢管理服務

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**原因：** 使用-Deployment 參數。

**修正:** -部署參數只能由部署系統管理員使用。 這些系統管理員通常是遠端桌面服務/Windows 虛擬桌面小組的成員。 以-TenantName \<TenantName > 取代-Deployment 參數。

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Error:RdsRoleAssignment--使用者未獲授權查詢管理服務

**原因 1：** 使用的帳戶沒有租使用者的遠端桌面服務擁有者許可權。

**修正 1:** 具有遠端桌面服務擁有者許可權的使用者必須執行角色指派。

**原因 2：** 正在使用的帳戶具有遠端桌面服務擁有者許可權, 但不屬於租使用者的 Azure Active Directory, 或沒有許可權可查詢使用者所在的 Azure Active Directory。

**修正 2:** 具有 Active Directory 許可權的使用者必須執行角色指派。

>[!Note]
>RdsRoleAssignment 無法將許可權授與 Azure Active Directory (AD) 中不存在的使用者。

## <a name="next-steps"></a>後續步驟

- 如需疑難排解 Windows 虛擬桌面和擴大追蹤的總覽, 請參閱[疑難排解總覽、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立租使用者和主機集區的問題進行疑難排解, 請參閱[建立租使用者和主機集](troubleshoot-set-up-issues.md)區。
- 若要在 Windows 虛擬桌面中設定虛擬機器 (VM) 時針對問題進行疑難排解, 請參閱[工作階段主機虛擬機器](troubleshoot-vm-configuration.md)設定。
- 若要疑難排解 Windows 虛擬桌面用戶端連線的問題, 請參閱[遠端桌面用戶端連接](troubleshoot-client-connection.md)。
- 若要深入瞭解此服務，請參閱[Windows 虛擬桌面環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)進行疑難排解。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
