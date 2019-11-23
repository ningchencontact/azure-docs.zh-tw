---
title: Configure group settings using PowerShell - Azure AD | Microsoft Docs
description: 如何使用 Azure Active Directory Cmdlet 管理群組的設定
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef0bfcb8c82d3f3caf90500e8852ca9e02c725aa
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382980"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>設定群組設定的 Azure Active Directory Cmdlet

本文包含使用 Azure Active Directory (Azure AD) PowerShell Cmdlet 以建立和更新群組的指示。 本內容僅適用於 Office 365 群組 (又稱為整合群組)。

> [!IMPORTANT]
> 某些設定需要 Azure Active Directory Premium P1 授權。 如需詳細資訊，請參閱[範本設定](#template-settings)資料表。

如需有關如何防止非系統管理員的使用者建立安全性群組的詳細資訊，請依照 [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) \(英文\) 中所述的內容設定  `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False`。

Office 365 群組設定是使用 Settings 物件和 SettingsTemplate 物件所設定。 一開始，您在目錄中不會看到任何設定物件，因為已使用預設設定來設定您的目錄。 若要變更預設設定，您必須使用設定範本來建立新的設定物件。 設定範本是由 Microsoft 所定義。 有數個不同的設定範本。 若要設定目錄的 Office 365 群組設定，您要使用名為 "Group.Unified" 的範本。 若要在單一群組上設定 Office 365 群組設定，請使用名為 "Group.Unified.Guest" 的範本。 此範本是用來管理 Office 365 群組的來賓存取權。 

Cmdlet 是 Azure Active Directory PowerShell V2 模組的一部分。 如需有關如何在電腦上下載及安裝模組的指示，請參閱 [Azure Active Directory PowerShell 第 2 版](https://docs.microsoft.com/powershell/azuread/)文章。 您可以從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureAD/)安裝第 2 版的模組。

## <a name="install-powershell-cmdlets"></a>安裝 PowerShell Cmdlet

請務必要將 Windows PowerShell 的任何舊版 Azure Active Directory PowerShell for Graph 模組解除安裝，並安裝 [Azure Active Directory PowerShell for Graph - 公開預覽版本 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)，然後才執行 PowerShell 命令。

1. 以系統管理理員身分開啟 Windows PowerShell 應用程式。
2. 將任何舊版的 AzureADPreview 解除安裝。
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. 安裝最新版的 AzureADPreview。
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>建立目錄層級的設定
這些步驟會建立目錄層級的設定，而套用至目錄中的所有 Office 365 群組。 Get-AzureADDirectorySettingTemplate Cmdlet 只能在[適用於圖表的 Azure AD PowerShell 預覽模組](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) \(英文\) 中取得。

1. 在 DirectorySettings Cmdlet 中，您必須指定需要使用的 SettingsTemplate 識別碼。 如果您不知道此識別碼，這個 Cmdlet 會傳回所有設定範本的清單：
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   這個 Cmdlet 呼叫會傳回所有可用的範本︰
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. 若要新增使用方針 URL，首先您需要取得 SettingsTemplate 物件，此物件會定義使用方針 URL 值；也就是 Group.Unified 範本：
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. 接下來，根據該範本建立新的設定物件：
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. 然後更新使用方針值：
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Then apply the setting:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. You can read the values using:

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>更新目錄層級的設定
To update the value for UsageGuideLinesUrl in the setting template, simply edit the URL with Step 4 above, then perform Step 5 to set the new value.

To remove the value of UsageGuideLinesUrl, edit the URL to be an empty string using Step 4 above:

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Then perform Step 5 to set the new value.

## <a name="template-settings"></a>範本設定
以下是 Group.Unified SettingsTemplate 中定義的設定。 除非另行指定，否則這些功能都需要 Azure Active Directory Premium P1 授權。 

| **設定** | **說明** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>類型：布林值<li>預設值︰True |此旗標指出是否允許非管理使用者在目錄中建立 Office 365 群組。 此設定不需要 Azure Active Directory Premium P1 授權。|
|  <ul><li>GroupCreationAllowedGroupId<li>類型：字串<li>預設值：“” |即使 EnableGroupCreation == false，仍允許成員建立 Office 365 群組之安全性群組的 GUID。 |
|  <ul><li>UsageGuidelinesUrl<li>類型：字串<li>預設值：“” |群組使用方針的連結。 |
|  <ul><li>ClassificationDescriptions<li>類型：字串<li>預設值：“” | 分類說明的以逗號分隔清單。 ClassificationDescriptions 的值只能採用下列格式：<br>$setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description"<br>where Classification matches the strings in the ClassificationList.<br>This setting does not apply when EnableMIPLabels == True.|
|  <ul><li>DefaultClassification<li>類型：字串<li>預設值：“” | 如果尚未指定，則是做為群組預設分類的分類。<br>This setting does not apply when EnableMIPLabels == True.|
|  <ul><li>PrefixSuffixNamingRequirement<li>類型：字串<li>預設值：“” | 長度上限為 64 個字元的字串，用以定義為 Office 365 群組設定的命名慣例。 如需詳細資訊，請參閱[對 Office 365 群組強制執行命名原則 (預覽)](groups-naming-policy.md)。 |
| <ul><li>CustomBlockedWordsList<li>類型：字串<li>預設值：“” | 使用者在群組名稱或別名中不允許使用之片語的逗號分隔字串。 如需詳細資訊，請參閱[對 Office 365 群組強制執行命名原則 (預覽)](groups-naming-policy.md)。 |
| <ul><li>EnableMSStandardBlockedWords<li>類型：布林值<li>預設值：“False” | 請勿使用
|  <ul><li>AllowGuestsToBeGroupOwner<li>類型：布林值<li>預設值︰False | 布林值，表示來賓使用者是否可以是群組的擁有者。 |
|  <ul><li>AllowGuestsToAccessGroups<li>類型：布林值<li>預設值︰True | 用以指出來賓使用者是否可存取 Office 365 內容的布林值。  此設定不需要 Azure Active Directory Premium P1 授權。|
|  <ul><li>GuestUsageGuidelinesUrl<li>類型：字串<li>預設值：“” | 來賓使用指導方針的連結 url。 |
|  <ul><li>AllowToAddGuests<li>類型：布林值<li>預設值︰True | 布林值表示是否允許將來賓新增至此目錄。 <br>This setting may be overridden and become read-only if *EnableMIPLabels* is set to *True* and a guest policy is associated with the sensitivity label assigned to the group. |
|  <ul><li>ClassificationList<li>類型：字串<li>預設值：“” |可套用至 Office 365 群組、以逗號分隔的有效分類值清單。 <br>This setting does not apply when EnableMIPLabels == True.|
|  <ul><li>EnableMIPLabels<li>類型：布林值<li>預設值：“False” |The flag indicating whether sensitivity labels published in Microsoft 365 Compliance Center can be applied to Office 365 Groups. For more information, see [Assign Sensitivity Labels for Office 365 groups](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Example: Configure Guest policy for groups at the directory level
1. Get all the setting templates:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. To set guest policy for groups at the directory level, you need Group.Unified template
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. 接下來，根據該範本建立新的設定物件：
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Then update AllowAddGuests setting
   ```powershell
   $Setting["AllowAddGuests"] = $False
   ```  
5. Then apply the setting:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. You can read the values using:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>讀取目錄層級的設定

如果您知道需要擷取的設定名稱，可以使用以下 Cmdlet 來擷取目前的設定值。 在此範例中，我們會擷取名為 "UsageGuidelinesUrl" 的設定值。 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
這些步驟會讀取目錄層級的設定，其會套用至目錄中的所有 Office 群組。

1. 讀取所有現有的目錄設定：
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   此 Cmdlet 會傳回所有目錄設定的清單：
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. 讀取特定群組的所有設定：
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Read all directory settings values of a specific directory settings object, using Settings ID GUID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   此 Cmdlet 會傳回針對此特定群組的這個設定物件中的名稱和值：
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>移除目錄層級的設定
這個步驟會移除目錄層級的設定，其會套用至目錄中的所有 Office 群組。
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Create settings for a specific group

1. 搜尋名為「Groups.Unified.Guest」的設定範本
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. 擷取 Groups.Unified.Guest 範本的範本物件：
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. 從範本建立新的設定物件︰
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. 將設定設為必要值︰
   ```powershell
   $SettingCopy["AllowAddGuests"]=$False
   ```
5. Get the ID of the group you want to apply this setting to:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. 在目錄中建立必要群組的新設定︰
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. To verify the settings, run this command:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>更新特定群組的設定
1. Get the ID of the group whose setting you want to update:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Retrieve the setting of the group:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Update the setting of the group as you need, e.g.
   ```powershell
   $Setting["AllowAddGuests"] = $True
   ```
4. Then get the ID of the setting for this specific group:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   You will get a response similar to this:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Then you can set the new value for this setting:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. You can read the value of the setting to make sure it has been updated correctly:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet 語法參考
您可以在 [Azure Active Directory Cmdlet](/powershell/azure/install-adv2?view=azureadps-2.0)中找到更多 Azure Active Directory PowerShell 文件。

## <a name="additional-reading"></a>其他閱讀資料

* [使用 Azure Active Directory 群組管理資源的存取權](../fundamentals/active-directory-manage-groups.md)
* [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
