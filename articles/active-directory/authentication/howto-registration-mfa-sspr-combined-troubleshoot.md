---
title: Azure AD SSPR 和 Multi-factor Authentication （預覽）-Azure Active Directory 的疑難排解合併的註冊
description: 針對 Azure AD 多重要素驗證和自助式密碼重設結合註冊 （預覽） 進行疑難排解
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba77772352d3f6f6494abeddc7faf9f12e5f80c2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262562"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>疑難排解合併安全性資訊註冊 （預覽）

這篇文章中的資訊是要引導系統管理員會針對合併的註冊體驗的使用者所報告的問題進行疑難排解。

|     |
| --- |
| 合併的安全性資訊註冊 Azure Multi-factor Authentication 和 Azure Active Directory (Azure AD) 自助式密碼重設為 Azure ad 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="audit-logs"></a>稽核記錄

合併的註冊記錄的事件是 Azure AD 中的 驗證方法的類別目錄中稽核記錄檔。

![Azure AD 稽核記錄顯示註冊事件的介面](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

下表列出所合併的註冊產生的所有稽核事件：

| 活動 | 狀態 | 原因 | 描述 |
| --- | --- | --- | --- |
| 使用者已註冊的所有必要的安全性資訊 | 成功 | 使用者已註冊的所有必要的安全性資訊。 | 當使用者成功完成註冊，就會發生此事件。|
| 使用者已註冊的所有必要的安全性資訊 | 失敗 | 使用者已取消的安全性資訊註冊。 | 使用者取消註冊從中斷模式時，就會發生此事件。|
| 使用者註冊安全性資訊 | 成功 | 使用者註冊*方法*。 | 當使用者註冊個別的方法，就會發生此事件。 *方法*可以驗證器應用程式、 電話、 電子郵件、 安全性問題、 應用程式密碼、 備用電話，等等。| 
| 檢閱使用者的安全性資訊 | 成功 | 使用者通過檢閱安全性資訊。 | 當使用者選取時，就會發生此事件**看起來沒問題**檢閱安全性資訊 頁面上。|
| 檢閱使用者的安全性資訊 | 失敗 | 使用者無法檢閱安全性資訊。 | 當使用者選取時，就會發生此事件**看起來沒問題**上的安全性資訊檢閱 頁面，但發生失敗後端。|
| 已刪除使用者的安全性資訊 | 成功 | 已刪除使用者*方法*。 | 當使用者刪除個別的方法，就會發生此事件。 *方法*可以驗證器應用程式、 電話、 電子郵件、 安全性問題、 應用程式密碼、 備用電話，等等。|
| 已刪除使用者的安全性資訊 | 失敗 | 使用者無法刪除*方法*。 | 當使用者嘗試刪除方法，但嘗試因故失敗時，就會發生此事件。 *方法*可以驗證器應用程式、 電話、 電子郵件、 安全性問題、 應用程式密碼、 備用電話，等等。|
| 使用者已變更預設的安全性資訊 | 成功 | 使用者已變更的預設安全性資訊*方法*。 | 當使用者變更的預設方法，就會發生此事件。 *方法*可以驗證器應用程式通知，從我的 authenticator 應用程式或權杖，呼叫 + X XXXXXXXXXX，文字的程式碼的程式碼 + X XXXXXXXXX，等等。|
| 使用者已變更預設的安全性資訊 | 失敗 | 使用者無法變更預設的安全性資訊，如*方法*。 | 當使用者嘗試變更的預設方法，但嘗試因故失敗時，就會發生此事件。 *方法*可以驗證器應用程式通知，從我的 authenticator 應用程式或權杖，呼叫 + X XXXXXXXXXX，文字的程式碼的程式碼 + X XXXXXXXXX，等等。|

## <a name="troubleshooting-interrupt-mode"></a>疑難排解中斷模式

| 徵狀 | 疑難排解步驟 |
| --- | --- |
| 我沒看到我預計看見的方法。 | 1.檢查使用者是否有 Azure AD 系統管理員角色。 如果是，檢視的 SSPR 系統管理員原則差異。 <br> 2.判斷使用者是否因為強制 Multi-factor Authentication 註冊或 SSPR 註冊強制中斷。 請參閱[流程圖](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes)」 結合註冊模式 」 下若要判斷應顯示哪些方法。 <br> 3.判斷多重要素驗證或 SSPR 原則最近已變更。 最新變更時，可能需要一些時間才能傳播更新的原則。|

## <a name="troubleshooting-manage-mode"></a>疑難排解管理模式

| 徵狀 | 疑難排解步驟 |
| --- | --- |
| 我沒有新增的特定方法的選項。 | 1.判斷方法是否已啟用多重要素驗證或 sspr。 <br> 2.如果已啟用的方法，再次儲存原則，並等候 1-2 小時再測試一次。 <br> 3.如果已啟用的方法，請確定使用者沒有已設定允許他們設定該方法的最大數目。|

## <a name="disable-combined-registration"></a>停用合併的註冊

當使用者的電話號碼和/或行動裝置應用程式中註冊新結合經驗，我們的服務戳記一組旗標 (StrongAuthenticationMethods) 對該使用者的方法。 這項功能可讓使用者使用這些方法執行 Multi-factor Authentication，需要多重要素驗證時。

如果系統管理員可讓預覽全新的體驗，透過註冊的使用者，然後系統管理員停用預覽，使用者可能不知情的情況下註冊 Multi-factor authentication 也。

如果已完成合併的註冊的使用者會在目前的自助式密碼重設 (SSPR) 註冊頁面[ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup)，將會提示使用者執行多重要素驗證，才能存取該頁面。 此步驟應該是從技術觀點來看，但它是新的使用者先前已註冊 sspr 只。 雖然這個額外步驟的確改善使用者的安全性狀態，藉由提供的安全性層級，系統管理員可能會想要回復其使用者，使它們不再能夠執行多重要素驗證。  

### <a name="how-to-roll-back-users"></a>如何復原使用者

如果您身為管理員，想要重設使用者的 Multi-factor Authentication 設定，您可以使用下一節中提供的 PowerShell 指令碼。 指令碼將會清除使用者的行動裝置應用程式和/或電話號碼的 StrongAuthenticationMethods 屬性。 如果您執行此指令碼，為您的使用者，他們必須在需要時重新註冊多重要素驗證。 我們建議搭配一或兩個使用者的測試復原之前先回復所有受影響的使用者。

請依照下列步驟可協助您復原使用者群組。

#### <a name="prerequisites"></a>必要條件

1. 安裝適當的 Azure AD PowerShell 模組。 在 PowerShell 視窗中，執行下列命令來安裝模組：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 儲存到電腦的受影響的使用者物件識別碼的清單，為每行一個識別碼的文字檔案。 記下檔案的所在位置。
1. 將下列指令碼儲存至您的電腦，並記下指令碼的位置：

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>復原

在 PowerShell 視窗中，執行下列命令，並提供的指令碼和使用者的檔案位置。 出現提示時，輸入全域管理員認證。 該指令碼將輸出每個使用者更新作業的結果。

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>停用預覽體驗

若要停用使用者的預覽體驗，請完成下列步驟：

1. 以全域系統管理員或使用者系統管理員身分登入 Azure 入口網站。
2. 移至**Azure Active Directory** > **使用者設定** > **管理的存取面板的預覽功能設定**。
3. 底下**使用者可以使用預覽功能註冊和管理的安全性資訊**，選取器設定為**無**，然後選取**儲存**。

若要註冊使用預覽版體驗時，將不會再提示使用者。

## <a name="next-steps"></a>後續步驟

* [深入了解合併的註冊自助式密碼重設和 Azure Multi-factor Authentication 的公開預覽](concept-registration-mfa-sspr-combined.md)
