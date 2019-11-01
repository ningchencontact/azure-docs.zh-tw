---
title: Azure PowerShell 指令碼範例 - 建立 Log Analytics 工作區 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 建立 Log Analytics 工作區
ms.service: azure-monitor
ms.subservice: logs
ms.topic: sample
author: MGoedtel
ms.author: magoedte
ms.date: 09/07/2017
ms.openlocfilehash: 6aad6c6f0656bd317a808de13e340d5774cea49b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931904"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>使用 PowerShell 建立 Log Analytics 工作區

如果您想要開始收集資料，並對資料作分析及採取行動，這個指令碼可讓您快速啟動並執行必要的 Azure Log Analytics 工作區。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>指令碼說明

這個指令碼會使用下列命令，在您的訂用帳戶中建立新的 Log Analytics 工作區。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | 取得現有工作區的相關資訊。 |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | 在指定的資源群組和位置建立工作區。 |


## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

