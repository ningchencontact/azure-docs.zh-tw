---
title: 指派和移除具有 Azure PowerShell Azure Active Directory 的系統管理員角色指派 |Microsoft Docs
description: 對於經常管理角色指派的人員, 您現在可以使用 Azure PowerShell 管理 Azure AD 系統管理員角色的成員。
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
ms.openlocfilehash: aa4bddf84720265afe361dff665f10ff8184f6f6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706492"
---
# <a name="assign-azure-active-directory-admin-roles-using-powershell"></a>使用 PowerShell 指派 Azure Active Directory 系統管理員角色

您可以使用 Azure PowerShell, 將角色指派給使用者帳戶的作業自動化。 本文使用[Azure Active Directory PowerShell 第2版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)模組。

## <a name="prepare-powershell"></a>準備 PowerShell

首先，您必須 [下載 Azure AD PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD/)。

## <a name="install-the-azure-ad-powershell-module"></a>安裝 Azure AD PowerShell 模組

若要安裝 AzureAD PowerShell 模組，請使用下列命令︰

```powershell
install-module azuread
import-module azuread
```

若要確認此模組已可使用，請使用下列命令︰

```powershell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

現在您可以開始在模組中使用 Cmdlet。 如需有關 Azure AD 模組中各式 Cmdlet 的完整描述，請參閱 [Azure Active Directory PowerShell 第 2 版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)的線上參考文件。

## <a name="permissions-required"></a>所需的權限

使用全域系統管理員帳戶連接到您的 Azure AD 租使用者, 以指派或移除角色。

## <a name="assign-a-single-role"></a>指派單一角色

若要指派角色, 您必須先取得其顯示名稱和您所指派之角色的名稱。 當您擁有帳戶的顯示名稱和角色的名稱時, 請使用下列 Cmdlet 將角色指派給使用者。

``` PowerShell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="assign-a-role-to-a-service-principal"></a>將角色指派給服務主體

將服務主體指派給角色的範例。

```powershell
# Fetch a service principal to assign to role
$roleMember = Get-AzureADServicePrincipal -ObjectId "00221b6f-4387-4f3f-aa85-34316ad7f956"
 
#Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole
 
# Fetch a directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"
 
# Add user to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
 
# Fetch the assignment for the role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADServicePrincipal
```

## <a name="multiple-role-assignments"></a>多角色指派

一次指派和移除多個角色的範例。

```powershell
#File name
$fileName="<file path>" 
 
$input_Excel = New-Object -ComObject Excel.Application
$input_Workbook = $input_Excel.Workbooks.Open($fileName)
$input_Worksheet = $input_Workbook.Sheets.Item(1)
 
        #Count number of users who have to be assigned to role
$count = $input_Worksheet.UsedRange.Rows.Count
 
#Loop through each line of the csv file starting from line 2 (assuming first line is title)
for ($i=2; $i -le $count; $i++)
{
       #Fetch user display name
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       
       #Fetch role name
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       #Assign role
       Add-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -RefObjectId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
 
#Remove multiple role assignments
for ($i=2; $i -le $count; $i++)
{
       $displayName = $input_Worksheet.Cells.Item($i,1).Text
       $roleName = $input_Worksheet.Cells.Item($i,2).Text
 
       Remove-AzureADDirectoryRoleMember -ObjectId (Get-AzureADDirectoryRole | Where-Object DisplayName -eq $roleName).ObjectId -MemberId (Get-AzureADUser | Where-Object DisplayName -eq $displayName).ObjectId
}
```

## <a name="remove-a-role-assignment"></a>移除角色指派

這個範例會移除指定使用者的角色指派。

```powershell
# Fetch user to assign to role
$roleMember = Get-AzureADUser -ObjectId "username@contoso.com"

#Fetch list of all directory roles with object id
Get-AzureADDirectoryRole

# Fetch a directory role by id
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Remove user from role
Remove-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -MemberId $roleMember.ObjectId 

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser

```

## <a name="next-steps"></a>後續步驟

* 歡迎在 [Azure AD 系統管理角色論壇](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上與我們共用資訊。
* 如需角色和管理員角色指派的詳細資訊，請參閱[指派管理員角色](directory-assign-admin-roles.md)。
* 如需預設使用者權限，請參閱[預設來賓和成員使用者權限的比較](../fundamentals/users-default-permissions.md)。
