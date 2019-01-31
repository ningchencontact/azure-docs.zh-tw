---
title: 部署本機代理程式 | Microsoft Docs
description: 為 Azure Stack 驗證即服務部署本機代理程式。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 23bbcbf6947100db26f31562c44f8073e16e986f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239336"
---
# <a name="deploy-the-local-agent"></a>部署本機代理程式

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用驗證即服務 (VaaS) 本機代理程式檢查您的硬體。 必須先將本機代理程式部署在要驗證的 Azure Stack 解決方案上，才能執行驗證測試。

> [!Note]  
> 您必須確定本機代理程式執行所在的機器並未失去網際網路的輸出存取能力。 只有已獲授權代表您的租用戶使用 VaaS 的使用者，才可存取這部機器。

若要部署本機代理程式：

1. 安裝本機代理程式
2. 執行例行性檢查
3. 執行本機代理程式

## <a name="download-and-start-the-local-agent"></a>下載並啟動本機代理程式

請將代理程式下載到資料中心內的機器，且此機器必須符合必要條件、不屬於 Azure Stack 系統的一部分，但可存取所有的 Azure Stack 端點。

### <a name="machine-prerequisites"></a>機器的必要條件

請確認您的機器符合下列準則：

- 可存取所有 Azure Stack 端點
- 已安裝 .NET 4.6 和 PowerShell 5.0
- 至少有 8 GB 的 RAM
- 至少有 8 核心處理器
- 至少有 200 GB 的磁碟空間
- 穩定的網際網路連線

Azure Stack 是待測系統。 機器不應為 Azure Stack 的一部分，或裝載在 Azure Stack 雲端中。

### <a name="download-and-install-the-agent"></a>下載並安裝代理程式

1. 在您要用來執行測試的機器上，透過已提升權限的提示字元開啟 Windows PowerShell。
2. 執行下列命令以下載本機代理程式：

    ```PowerShell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. 執行下列命令以安裝本機代理程式相依性：

    ```PowerShell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **參數**

    | 參數 | 說明 |
    | --- | --- |
    | aadServiceAdminUser | Azure AD 租用戶的全域管理員使用者。 例如，它可以是 vaasadmin@contoso.onmicrosoft.com。 |
    | aadServiceAdminPassword | 全域管理員使用者的密碼。 |
    | AadTenantId | 向驗證即服務註冊之 Azure 帳戶的 Azure AD 租用戶識別碼。 |
    | ExternalFqdn | 您可以從組態檔中取得完整網域名稱。 如需指示，請參閱 [Azure Stack 驗證即服務的工作流程一般參數](azure-stack-vaas-parameters.md)。 |
    | 區域 | Azure AD 租用戶的區域。 |

命令會下載公用映像存放庫 (PIR) 映像 (OS VHD)，並從 Azure Blob 儲存體複製到您的 Azure Stack 儲存體。

![下載必要條件](media/installingprereqs.png)

> [!Note]
> 如果您在下載這些映像時遇到網路速度緩慢的問題，請個別將其下載至本機共用，並指定參數 **-LocalPackagePath** *FileShareOrLocalPath*。 您可以在[對驗證即服務進行疑難排解](azure-stack-vaas-troubleshoot.md)的[處理緩慢的網路連線](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)一節中，找到更多關於 PIR 下載的指引。

## <a name="checks-before-starting-the-tests"></a>開始測試前的檢查

測試會執行遠端動作。 執行測試的機器必須能夠存取 Azure Stack 端點，否則測試將無法執行。 如果您使用 VaaS 本機代理程式，請使用將作為代理程式執行位置的機器。 您可以執行下列檢查以確認您的機器是否能夠存取 Azure Stack 端點：

1. 檢查是否能夠連線至「基底 URI」。 開啟 CMD 提示字元或 Bash 殼層，然後執行下列命令 (請將 `<EXTERNALFQDN>` 取代為環境的外部 FQDN)：

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. 開啟網頁瀏覽器並移至 `https://adminportal.<EXTERNALFQDN>`，以檢查是否能夠連線至 MAS 入口網站。

3. 使用建立測試進行時所提供的 Azure AD 服務管理員名稱和密碼值來進行登入。

4. 依照[執行 Azure Stack 的驗證測試](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostic-test)中的說明執行 **Test-AzureStack** PowerShell Cmdlet，以檢查系統的健康情況。 在啟動任何測試之前，請先修正警告和錯誤。

## <a name="run-the-agent"></a>執行代理程式

1. 在已提升權限的提示字元中開啟 Windows PowerShell。

2. 執行以下命令：

    ```PowerShell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **參數**  
    | 參數 | 說明 |
    | --- | --- |
    | VaaSUserId | 用來登入 VaaS 入口網站的使用者識別碼 (例如 UserName@Contoso.com) |
    | VaaSTenantId | 向驗證即服務註冊之 Azure 帳戶的 Azure AD 租用戶識別碼。 |

    > [!Note]  
    > 當您執行代理程式時，目前的工作目錄必須是工作引擎主機執行檔 **Microsoft.VaaSOnPrem.TaskEngineHost.exe** 的所在位置。

如果您未看到任何回報的錯誤，表示本機代理程式已成功執行。 下列範例文字會出現在主控台視窗上。

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![已啟動的代理程式](media/startedagent.png)

代理程式有唯一的名稱可資識別。 根據預設，它會使用其啟動所在之機器的完整網域名稱 (FQDN)。 您必須將視窗最小化，以避免不慎選取視窗，因為變更焦點會導致所有其他動作暫停。

## <a name="next-steps"></a>後續步驟

- [針對驗證即服務進行疑難排解](azure-stack-vaas-troubleshoot.md)
- [驗證即服務的重要概念](azure-stack-vaas-key-concepts.md)
- [快速入門：使用驗證即服務入口網站來排程第一個測試](azure-stack-vaas-schedule-test-pass.md)