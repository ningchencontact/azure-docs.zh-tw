---
title: Azure AD SSPR 和 MFA （預覽）-Azure Active Directory 的疑難排解合併的註冊
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
ms.openlocfilehash: 8f799b671e6216b402aa988aa66da4c2cfc44693
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317522"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>疑難排解合併安全性資訊註冊 （預覽）

這篇文章中所提供的資訊可以引導系統管理員與終端使用者所回報的合併的註冊體驗疑難排解問題。

|     |
| --- |
| 合併的安全性資訊註冊 Azure Multi-factor Authentication 與 Azure AD 自助式密碼重設為 Azure Active directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="audit-logs"></a>稽核記錄

合併的註冊記錄的事件是 Azure AD 中的 驗證方法 類別下稽核記錄檔。

![Azure AD 稽核記錄檔的目錄中顯示一些安全性資訊註冊事件，新的使用者介面](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

以下列出所合併的註冊產生的所有稽核事件：

| 活動 | 狀態 | 原因 | 描述 |
| --- | --- | --- | --- |
| 使用者已註冊的所有必要的安全性資訊 | 成功 | 使用者已註冊的所有必要的安全性資訊。 | 當使用者成功完成註冊，就會發生此事件。|
| 使用者已註冊的所有必要的安全性資訊 | 失敗 | 使用者已取消的安全性資訊註冊。 | 使用者取消註冊從中斷模式時，就會發生此事件。|
| 使用者註冊安全性資訊 | 成功 | 使用者已註冊 「 方法 」。 | 當使用者註冊個別的方法，就會發生此事件。 「 方法 」 可以是驗證器應用程式、 電話、 電子郵件、 安全性問題、 應用程式密碼、 備用電話等。| 
| 檢閱使用者的安全性資訊 | 成功 | 使用者通過檢閱安全性資訊。 | 當使用者按一下 [檢閱安全性資訊] 頁面上的 「 狀況良好 」，就會發生此事件。|
| 檢閱使用者的安全性資訊 | 失敗 | 使用者無法檢閱安全性資訊。 | 此事件，就會發生當使用者按一下 「 良好 」 上的安全性資訊檢閱 頁面，但發生失敗後端中。|
| 已刪除使用者的安全性資訊 | 成功 | 使用者已刪除 「 方法 」。 | 當使用者刪除個別的方法，就會發生此事件。 「 方法 」 可以是驗證器應用程式、 電話、 電子郵件、 安全性問題、 應用程式密碼、 備用電話等。|
| 已刪除使用者的安全性資訊 | 失敗 | 使用者無法刪除 「 方法 」。 | 當使用者嘗試刪除方法，但它因為某些原因而失敗時，就會發生此事件。 「 方法 」 可以是驗證器應用程式、 電話、 電子郵件、 安全性問題、 應用程式密碼、 備用電話等。|
| 使用者已變更預設的安全性資訊 | 成功 | 使用者會變成 「 方法 」 中的預設安全性資訊。 | 當使用者變更其預設方法，就會發生此事件。 「 方法 」，驗證器應用程式通知，來自我的 authenticator 應用程式或權杖，呼叫 + X XXXXXXXXXX，文字的程式碼的程式碼到 + X XXXXXXXXX 等等。|
| 使用者已變更預設的安全性資訊 | 失敗 | 使用者無法將預設的安全性資訊變更為 「 方法 」。 | 當使用者嘗試變更其預設方法，但它因為某些原因而失敗時，就會發生此事件。 驗證器應用程式通知，從我的 authenticator 應用程式或權杖，呼叫程式碼 + X XXXXXXXXXX，文字到 + X XXXXXXXXX 等程式碼，可以是 「 方法 」。|

## <a name="troubleshooting-interrupt-mode"></a>疑難排解中斷模式

| 徵狀 | 疑難排解步驟 |
| --- | --- |
| 我沒看到我預計看見的方法。 | 1.檢查使用者是否有 Azure AD 系統管理員角色。 如果是，請檢閱 SSPR 的系統管理員原則差異。 <br> 2.判斷使用者是否因為 MFA 註冊強制執行或 SSPR 註冊強制中斷。 檢閱此流程圖來判斷哪些方法應該會顯示合併的註冊模式下。 <br> 3.判斷如何最近的 MFA 或 SSPR 的原則已變更。 最新變更時，可能需要一些時間才能傳播更新的原則。|

## <a name="troubleshooting-manage-mode"></a>疑難排解管理模式

| 徵狀 | 疑難排解步驟 |
| --- | --- |
| 我沒有新增的特定方法的選項。 | 1.判斷方法是否已啟用 mfa 或 sspr。 <br> 2.如果已啟用的方法，重新儲存原則，並等候 1-2 小時再測試一次。 <br> 3.如果已啟用的方法，請確定使用者沒有已設定允許他們設定該方法的最大數目。|

## <a name="disable-combined-registration"></a>停用合併的註冊

當使用者註冊時其電話號碼和/或行動裝置應用程式在新結合經驗，我們的服務戳記一組旗標 (StrongAuthenticationMethods) 對該使用者的方法。 這項功能允許使用者在需要 MFA 時，使用這些方法執行 Azure Multi-Factor Authentication (MFA)。

使用者透過新體驗註冊的方法具有 StrongAuthenticationMethods 屬性集。 如果系統管理員啟用預覽、使用者透過新體驗註冊，然後系統管理員停用預覽，用戶可能會在不知不覺中註冊MFA。

如果已完成的使用者結合註冊瀏覽至目前的自助式密碼重設 (SSPR) 註冊頁面上，在[ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup)，提示他們執行 MFA，才能存取該頁面。 從技術觀點來看，此步驟是預期的行為，但對於先前僅註冊 SSPR 的使用者，此步驟會是新的行為。 雖然這個額外步驟會藉由提供一層額外的安全性改善使用者的安全性狀態，但系統管理員可能會想要復原其使用者，使它們不再能夠執行 MFA。  

### <a name="how-to-roll-back-users"></a>如何復原使用者

如果您作為系統管理員想要重設使用者的 MFA 設定，我們已建立 PowerShell 指令碼，該指令碼將清除使用者的行動裝置應用程式和/或電話號碼的 StrongAuthenticationMethods 屬性。 為您的使用者執行這個指令碼表示他們必須視需要重新註冊 MFA。 我們建議在復原所有受影響的使用者之前，使用一或兩個使用者測試復原。

下列步驟可協助您復原使用者群組：

#### <a name="prerequisites"></a>必要條件

1. 您必須安裝適當的 Azure AD PowerShell 模組。 在 PowerShell 視窗中，執行下列命令來安裝模組：

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 將受影響的使用者物件識別碼/識別碼清單，以每行一個識別碼的文字檔儲存到您的電腦。 記下檔案的所在位置。
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

在 PowerShell 視窗中，在更新反白顯示的位置之後執行下列命令。 出現提示時，輸入全域管理員認證。 該指令碼將輸出每個使用者更新作業的結果。

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>停用預覽體驗

若要停用使用者的預覽體驗，請完成下列步驟：

1. 以全域系統管理員或使用者系統管理員身分登入 Azure 入口網站。
2. 瀏覽至 [Azure Active Directory]、[使用者設定]、[管理存取面板預覽功能的設定]。
3. 在 [使用者可以使用預覽功能註冊與管理安全性資訊] 底下，將選取器設定為 [無]，然後按一下 [儲存]。

將不再提示使用者使用預覽體驗進行註冊。

## <a name="next-steps"></a>後續步驟

[深入了解合併的註冊自助式密碼重設和 Azure Multi-factor Authentication 的公開預覽](concept-registration-mfa-sspr-combined.md)
