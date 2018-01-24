---
title: "在 Azure Stack 中輪替使用祕密 | Microsoft Docs"
description: "了解如何在 Azure Stack 中輪替使用祕密。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>在 Azure Stack 中輪替使用祕密

「適用於：Azure Stack 整合系統」

定期更新您的 Azure Stack 元件密碼。

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>更新基礎板管理控制器 (BMC) 的密碼

基礎板管理控制器 (BMC) 可監視伺服器的實體狀態。 有關更新 BMC 密碼的規格和指示會隨著您的原始設備製造商 (OEM) 硬體廠商而有所不同。

1. 依照您的 OEM 指示，在 Azure Stack 的實體伺服器上更新 BMC。 您環境中每個 BMC 的密碼必須相同。
2. 在 Azure Stack 工作階段中開啟具有特殊權限的端點。 如需相關指示，請參閱[使用 Azure Stack 中具有特殊權限的端點](azure-stack-privileged-endpoint.md)。
3. 在您的 PowerShell 提示變更為 **[IP 位址或 ERCS VM 名稱]: PS>** 或變更為 **[azs-ercs01]: PS>** (取決於環境) 之後，藉由執行 `invoke-command` 來執行 `Set-BmcPassword`。 將具有特殊權限的端點工作階段變數當作參數傳送。 例如︰

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    您也可以使用靜態 PowerShell 版本搭配密碼，如以下程式碼行所示：
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>後續步驟

若要深入了解安全性和 Azure Stack，請參閱 [Azure Stack 基礎結構安全性狀態](azure-stack-security-foundations.md)。
