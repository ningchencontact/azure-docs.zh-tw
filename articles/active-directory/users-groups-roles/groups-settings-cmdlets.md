---
title: 使用 PowerShell 進行群組設定 - Azure Active Directory | Microsoft Docs
description: 如何使用 Azure Active Directory Cmdlet 管理群組的設定
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/10/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bd79b9a6fa8aedd45f41b64f8f81a908feab71f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882995"
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

## Create settings at the directory level
These steps create settings at directory level, which apply to all Office 365 groups in the directory. The Get-AzureADDirectorySettingTemplate cmdlet is available only in the [Azure AD PowerShell Preview module for Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. In the DirectorySettings cmdlets, you must specify the ID of the SettingsTemplate you want to use. If you do not know this ID, this cmdlet returns the list of all settings templates:
  
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
5. 然後套用設定：
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. 您可以使用來讀取這些值：

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>更新目錄層級的設定
若要更新設定範本中 UsageGuideLinesUrl 的值，只需編輯上述步驟4中的 URL，然後執行步驟5來設定新的值。

若要移除 UsageGuideLinesUrl 的值，請使用上述的步驟4，將 URL 編輯為空字串：

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
然後執行步驟5以設定新的值。

## <a name="template-settings"></a>範本設定
以下是 Group.Unified SettingsTemplate 中定義的設定。 除非另行指定，否則這些功能都需要 Azure Active Directory Premium P1 授權。 

| **設定** | **描述** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>輸入：Boolean<li>預設值：真 |此旗標指出是否允許非管理使用者在目錄中建立 Office 365 群組。 此設定不需要 Azure Active Directory Premium P1 授權。|
|  <ul><li>GroupCreationAllowedGroupId<li>輸入：String<li>預設值：“” |即使 EnableGroupCreation == false，仍允許成員建立 Office 365 群組之安全性群組的 GUID。 |
|  <ul><li>UsageGuidelinesUrl<li>輸入：String<li>預設值：“” |群組使用方針的連結。 |
|  <ul><li>ClassificationDescriptions<li>輸入：String<li>預設值：“” | 分類說明的以逗號分隔清單。 ClassificationDescriptions 的值只能採用下列格式：<br>$setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description"<br>其中分類會符合 ClassificationList 中的字串。|
|  <ul><li>DefaultClassification<li>輸入：String<li>預設值：“” | 如果尚未指定，則是做為群組預設分類的分類。|
|  <ul><li>PrefixSuffixNamingRequirement<li>輸入：String<li>預設值：“” | 長度上限為 64 個字元的字串，用以定義為 Office 365 群組設定的命名慣例。 如需詳細資訊，請參閱[對 Office 365 群組強制執行命名原則 (預覽)](groups-naming-policy.md)。 |
| <ul><li>CustomBlockedWordsList<li>輸入：String<li>預設值：“” | 使用者在群組名稱或別名中不允許使用之片語的逗號分隔字串。 如需詳細資訊，請參閱[對 Office 365 群組強制執行命名原則 (預覽)](groups-naming-policy.md)。 |
| <ul><li>EnableMSStandardBlockedWords<li>輸入：Boolean<li>預設值：“False” | 請勿使用
|  <ul><li>AllowGuestsToBeGroupOwner<li>輸入：Boolean<li>預設值：偽 | 布林值，表示來賓使用者是否可以是群組的擁有者。 |
|  <ul><li>AllowGuestsToAccessGroups<li>輸入：Boolean<li>預設值：真 | 用以指出來賓使用者是否可存取 Office 365 內容的布林值。  此設定不需要 Azure Active Directory Premium P1 授權。|
|  <ul><li>GuestUsageGuidelinesUrl<li>輸入：String<li>預設值：“” | 來賓使用指導方針的連結 url。 |
|  <ul><li>AllowToAddGuests<li>輸入：Boolean<li>預設值：真 | 布林值表示是否允許將來賓新增至此目錄。|
|  <ul><li>ClassificationList<li>輸入：String<li>預設值：“” |可套用至 Office 365 群組、以逗號分隔的有效分類值清單。 |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>範例：在目錄層級設定群組的來賓原則
1. 取得所有設定範本：
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. 若要在目錄層級設定群組的來賓原則，您需要群組. 整合範本
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. 接下來，根據該範本建立新的設定物件：
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. 然後更新 AllowToAddGuests 設定
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. 然後套用設定：
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. 您可以使用來讀取這些值：

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

3. 使用設定識別碼 GUID，讀取特定目錄設定物件的所有目錄設定值：
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
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>移除目錄層級的設定
這個步驟會移除目錄層級的設定，其會套用至目錄中的所有 Office 群組。
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>建立特定群組的設定

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
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. 取得您想要套用此設定之群組的識別碼：
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. 在目錄中建立必要群組的新設定︰
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. 若要驗證設定，請執行此命令：
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>更新特定群組的設定
1. 取得您想要更新其設定之群組的識別碼：
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. 取出群組的設定：
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. 視需要更新群組的設定，例如
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. 然後取得此特定群組之設定的識別碼：
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   您會收到類似下面的回應：
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. 接著，您可以設定此設定的新值：
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. 您可以讀取設定的值，以確定它已正確更新：
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet 語法參考
您可以在 [Azure Active Directory Cmdlet](/powershell/azure/install-adv2?view=azureadps-2.0)中找到更多 Azure Active Directory PowerShell 文件。

## <a name="additional-reading"></a>其他閱讀資料

* [使用 Azure Active Directory 群組來管理資源的存取權](../fundamentals/active-directory-manage-groups.md)
* [整合內部部署身分識別與 Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
