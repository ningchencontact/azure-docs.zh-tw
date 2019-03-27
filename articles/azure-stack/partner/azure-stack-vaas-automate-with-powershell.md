---
title: 使用 PowerShell 將 Azure Stack 驗證設為自動化 | Microsoft Docs
description: 您可以使用 PowerShell 將 Azure Stack 驗證設為自動化。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7f9a4ce4f1e16f69a1d8998e24c1bfe955d17d92
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767102"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>使用 PowerShell 將 Azure Stack 驗證設為自動化

驗證即服務 (VaaS) 可讓您使用 **LaunchVaaSTests.ps1** 指令碼自動啟動測試。

> [!NOTE]  
> 自動化僅適用於「測試成功」工作流程。 封裝驗證和驗證解決方案工作流程僅透過 VaaS 入口網站支援。

此指令碼可用來：

> [!div class="checklist"]
> * 安裝必要條件
> * 安裝並啟動本機代理程式
> * 啟動某類別的測試，例如*整合*、*功能性*、*可靠性*
> * 報告測試結果

## <a name="launch-the-test-pass-workflow"></a>啟動測試進行工作流程

1. 開啟提升權限的 PowerShell 提示字元。

2. 執行下列指令碼，以下載自動化指令碼：

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. 步驟 3：使用適當的參數值執行下列程式碼：

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **參數**

    | 參數 | 說明 |
    | --- | --- |
    | VaaSUserId | 您的 VaaS 使用者識別碼。 |
    | VaaSUserPassword | 您的 VaaS 密碼。 |
    | VaaSAccountTenantId | 您的 VaaS 租用戶 GUID。 |
    | VaaSSolutionName | 用以執行「測試進行」的 VaaS 解決方案名稱。 |
    | VaaSTestPassName | 要建立的 VaaS 測試進行工作流程的名稱。 |
    | VaaSTestCategories | `Integration`、`Functional` 或 `Reliability` 。 如果您使用多個值，請以逗號分隔每個值。  |
    | ServiceAdminUserName | 您的 Azure Stack 服務管理帳戶。  |
    | ServiceAdminPassword | 您的 Azure Stack 服務密碼。  |
    | TenantAdminUserName | 主要租用戶的系統管理員。  |
    | TenantAdminPassword | 主要租用戶的密碼。  |
    | CloudAdminUserName | 雲端管理員使用者名稱。  |
    | CloudAdminPassword | 雲端管理員的密碼。  |

4. 檢閱測試結果。 如需其他選項的相關資訊，請參閱[監視和管理 VaaS 入口網站中的測試](azure-stack-vaas-monitor-test.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Stack 上的 PowerShell，請檢閱最新的模組。

> [!div class="nextstepaction"]
> [Azure Stack 模組](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
