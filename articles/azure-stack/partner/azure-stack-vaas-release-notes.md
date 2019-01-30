---
title: Azure Stack 驗證即服務的版本資訊 | Microsoft Docs
description: 「Azure Stack 驗證即服務」的版本資訊。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: ce16ab3b723b9a6c87c7b980e3cc76c389609da6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847895"
---
# <a name="release-notes-for-validation-as-a-service"></a>驗證即服務的版本資訊

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

本文包含有「Azure Stack 驗證即服務」的版本資訊。

## <a name="version-405"></a>4.0.5 版
2019 年 1 月 17 日

-  更新磁碟識別測試以解決儲存體集區的不一致。 版本：5.1.14.0  -> 5.1.15.0
-  更新 Azure Stack 每月更新驗證以解決已核准軟體和內容驗證的不一致。 版本：5.1.14.0  -> 5.1.15.0
-  更新 OEM 延伸模組套件驗證以在 Azure Stack 更新步驟「之前」先執行必要的檢查。 版本：5.1.14.0 -> 5.1.15.0
-  內部錯誤修正



## <a name="version-402"></a>4.0.2 版

2019 年 1 月 7 日

如果您執行 Azure Stack 每月更新驗證工作流程，而且您的 OEM 更新套件版本不是 1810 或更高版本，您就會在進行 OEM 更新步驟時收到錯誤。 這是一個錯誤 (bug)。 正在開發修正程式。風險降低步驟如下所示：

1.  如往常一樣執行 OEM 更新。
2.  在成功套用套件之後執行 Test-AzureStack 並儲存輸出。
3.  取消測試。
4.  將儲存的輸出傳送至 VaaSHelp@microsoft.com，以接收回合的傳遞結果。

## <a name="version-402"></a>4.0.2 版

2018 年 11 月 30 日

- 內部錯誤修正

## <a name="version-401"></a>4.0.1 版

2018 年 10 月 8 日

- VaaS 先決條件

    `Install-VaaSPrerequisites` 已不再需要雲端管理員認證。 如果您執行此 Cmdlet 的最新版本，請參閱[下載並安裝代理程式](azure-stack-vaas-local-agent.md#download-and-install-the-agent)，以取得修訂過的命令來安裝先決條件。 命令如下：

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>4.0.0 版

2018 年 8 月 29 日

- VaaS 先決條件和 VHD 更新

    `Install-VaaSPrerequisites` 現在需要雲端管理員認證，以處理解決方案驗證期間的問題。 位於[下載並安裝代理程式](azure-stack-vaas-local-agent.md#download-and-install-the-agent)的文件已進行下列更新：

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > 指令碼所需的 `$CloudAdminCreds` 是針對要驗證的 Azure Stack 執行個體。 它們不是 VaaS 租用戶所使用的 Azure Active Directory 認證。

- 本機代理程式更新

    舊版本機代理程式與目前 4.0.0 版服務不相容。 所有使用者都必須更新其本機代理程式。 如需有關安裝最新代理程式的指示，請參閱[下載並安裝代理程式](azure-stack-vaas-local-agent.md#download-and-install-the-agent)。

- PowerShell 自動化更新

    針對需要最新版指令碼處理套件的 `LaunchVaaSTests` PowerShell 指令碼已進行變更。 如需有關安裝最新版指令碼處理套件的指示，請參閱[啟動測試進行工作流程](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow)。

- 驗證即服務入口網站

  - 套件簽署通知

    在「解決方案驗證」工作流程中一併提交 OEM 自訂套件時，系統會驗證套件格式，以確保格式遵守已發佈的規格。 如果套件不符合規範，執行就會失敗。 系統會將電子郵件通知傳送給租用戶的已註冊 Azure Active Directory 連絡人電子郵件地址。

  - 互動式測試分類

    已新增**互動式**測試分類。 這些測試可讓合作夥伴執行互動式、非自動化的 Azure Stack 案例。

  - 互動式功能驗證

    現在，在「測試進行」工作流程中已能夠針對特定功能提供焦點式意見反應。 `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` 測試會檢查是否已正確套用特定更新，然後收集意見反應。

## <a name="next-steps"></a>後續步驟

- [針對驗證即服務進行疑難排解](azure-stack-vaas-troubleshoot.md)
