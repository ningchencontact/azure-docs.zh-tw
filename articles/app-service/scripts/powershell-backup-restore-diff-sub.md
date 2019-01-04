---
title: Azure PowerShell 指令碼範例 - 將應用程式備份還原至另一個訂用帳戶 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 從另一個訂用帳戶中的備份還原 Web 應用程式
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 9e27585d44214a2826ca2c4ac705eb6bb803344a
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53586225"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>使用 PowerShell 從另一個訂用帳戶中的備份還原 Web 應用程式

此範例指令碼會從現有的 Web 應用程式擷取先前完成的備份，並將它還原到另一個訂用帳戶中的 Web 應用程式。 

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/overview) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzureRmAccount` 來建立與 Azure 的連線。 

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>清除部署 

如果您不再需要 Web 應用程式，請使用下列命令來移除資源群組、Web 應用程式和所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) | 新增已驗證的帳戶以用於 Azure Resource Manager Cmdlet 要求。  |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | 取得 Web 應用程式的備份清單。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | 建立 Web 應用程式 |
| [Restore-AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | 從先前完成的備份中還原 Web 應用程式。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 App Service Web Apps 的其他 Azure PowerShell 範例。
