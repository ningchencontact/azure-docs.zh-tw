---
title: 補救不符合規範的 Azure 自動化狀態設定伺服器
description: 如何視需要將設定重新套用至設定狀態已漂移的伺服器
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614494"
---
# <a name="remediate-non-compliant-dsc-servers"></a>補救不相容的 DSC 伺服器

當伺服器向 Azure 自動化狀態設定註冊時, [設定模式] 會設為 [ApplyOnly]、[ApplyandMonitor] 或 [ApplyAndAutoCorrect]。
如果模式未設定為 [自動校正], 則因任何原因而偏離相容狀態的伺服器將會保持不相容, 直到手動更正為止。

Azure 計算提供名為執行命令的功能, 可讓客戶在虛擬機器中執行腳本。
本檔提供手動修正設定漂移時, 這項功能的範例腳本。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>使用 PowerShell 更正 Windows 虛擬機器的漂移

如需在 Windows 虛擬機器上使用執行命令功能的逐步指示, 請參閱檔頁面[使用執行命令在您的 WINDOWS VM 中執行 PowerShell 腳本](/azure/virtual-machines/windows/run-command)。

若要強制 Azure 自動化狀態設定節點下載最新的設定並加以套用, 請使用`Update-DscConfiguration` Cmdlet。
如需詳細資訊, 請參閱 Cmdlet 檔[更新-update-dscconfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>修正 Linux 虛擬機器的漂移

Linux 伺服器目前不提供類似的功能。
唯一的選項是重複註冊程式。
針對 Azure 節點, 可以從入口網站或使用 Az Automation Cmdlet 來進行漂移校正。
如需此程式的詳細資料, 請閱讀將[電腦上線以進行管理 Azure 自動化狀態設定](/azure/automation/automation-dsc-onboarding#azure-portal)。
針對混合式節點, 可以使用包含的 Python 腳本來進行漂移校正。
請參閱[POWERSHELL DSC For Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)存放庫中的檔。

## <a name="next-steps"></a>後續步驟

- 如需 PowerShell Cmdlet 參考，請參閱 [Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
