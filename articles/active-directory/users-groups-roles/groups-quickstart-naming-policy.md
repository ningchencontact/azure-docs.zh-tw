---
title: Office 365 群組的新增命名原則快速入門 - Azure Active Directory | Microsoft Docs
description: 說明如何在 Azure Active Directory 中新增使用者或刪除現有的使用者
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f2219e038d3432807c81246256873a1ecb2cd9b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093468"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>快速入門：Azure Active Directory 中的群組命名原則

在本快速入門中，您將在 Azure Active Directory (Azure AD) 租用戶中為使用者建立的 Office 365 群組設定命名原則，以利排序和搜尋租用戶的群組。 例如，您可以使用命名原則來：

* 傳達群組的功能、成員資格、地理區域或群組的建立者。
* 將通訊錄中的群組分類。
* 防止在群組名稱和別名中使用特定字組。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="install-powershell-cmdlets"></a>安裝 PowerShell Cmdlet

請務必要將 Windows PowerShell 的任何舊版 Azure Active Directory PowerShell for Graph 模組解除安裝，並安裝 [Azure Active Directory PowerShell for Graph - 公開預覽版本 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)，然後才執行 PowerShell 命令。 

1. 以系統管理理員身分開啟 Windows PowerShell 應用程式。
2. 將任何舊版的 AzureADPreview 解除安裝。
  
   ```
   Uninstall-Module AzureADPreview
   ```
3. 安裝最新版的 AzureADPreview。
  
   ```
   Install-Module AzureADPreview
   ```
   如果系統發出有關所存取之存放庫不受信任的提示，請輸入 **Y**。新模組可能需要幾分鐘的時間才能安裝完成。

## <a name="set-up-naming-policy"></a>設定命名原則

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>步驟 1：使用 PowerShell Cmdlet 登入

1. 開啟 Windows PowerShell 應用程式。 您不需要提高的權限。

2. 執行下列命令，以準備執行 Cmdlet。
  
   ```
   Import-Module AzureADPreview
   Connect-AzureAD
   ```
   在所開啟的 [登入帳戶] 畫面中，輸入系統管理員帳戶和密碼以連線到服務，然後選取 [登入]。

3. 請遵循[用於進行群組設定的 Azure Active Directory Cmdlet](groups-settings-cmdlets.md) 中的步驟，建立此租用戶的群組設定。

### <a name="step-2-view-the-current-settings"></a>步驟 2：檢視目前的設定

1. 檢視目前的命名原則設定。
  
   ```
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
2. 顯示目前的群組設定。
  
   ```
   $Setting.Values
   ```
  
### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>步驟 3：設定命名原則和任何自訂封鎖字組

1. 在 Azure AD PowerShell 中設定群組名稱前置詞和後置詞。 設定中必須包含 [GroupName]，此功能才能正常運作。
  
   ```
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
2. 設定想要限制的自訂封鎖字組。 下列範例說明如何新增自己的自訂字組。
  
   ```
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
3. 儲存設定以使新原則生效，如下列範例所示。
  
   ```
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
就這麼簡單。 您已設定命名原則並新增自訂封鎖字組。

## <a name="clean-up-resources"></a>清除資源

1. 在 Azure AD PowerShell 中將群組名稱前置詞和後置詞設為空的。
  
   ```
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
2. 將自訂封鎖字組設為空的。
  
   ```
   $Setting["CustomBlockedWordsList"]=""
   ```
  
3. 儲存設定。
  
   ```
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 PowerShell Cmdlet 設定 Azure AD 租用戶的命名原則。

如需關於技術限制、新增自訂封鎖字組清單，或 Office 365 應用程式使用者體驗的詳細資訊，請繼續參考下一篇文章，以深入了解命名原則的詳細資料。
> [!div class="nextstepaction"]
> [命名原則的所有詳細資料](groups-naming-policy.md)
