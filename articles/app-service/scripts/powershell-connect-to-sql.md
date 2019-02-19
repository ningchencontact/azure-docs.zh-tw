---
title: Azure PowerShell 指令碼範例 - 將應用程式連線至 SQL 資料庫 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 將 App Service 應用程式連線至 SQL 資料庫
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 055440a9-fff1-49b2-b964-9c95b364e533
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: d0c38b69d8cb2fda85a85fd27f0a5b26da51b699
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106200"
---
# <a name="connect-an-app-service-app-to-a-sql-database"></a>將 App Service 應用程式連線至 SQL 資料庫

在此案例中，您會學習如何建立 Azure SQL Database 和 App Service 應用程式。 然後，您會使用應用程式設定將 SQL Database 連結到應用程式。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/overview) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzAccount` 來建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-sql/connect-to-sql.ps1?highlight=13 "Connect an app to a SQL database")]

## <a name="clean-up-deployment"></a>清除部署 

執行指令碼範例之後，您可以使用下列命令來移除資源群組、App Service 應用程式及所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 建立 App Service 方案。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 建立 App Service 應用程式。 |
| [New-AzSQLServer](/powershell/module/az.sql/new-azsqlserver) | 建立 SQL Database 伺服器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 建立 SQL Database 伺服器防火牆規則。 |
| [New-AzSQLDatabase](/powershell/module/az.sql/new-azsqldatabase) | 建立資料庫或彈性資料庫。 |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | 修改 App Service 應用程式的設定。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 Azure App Service 的其他 Azure PowerShell 範例。
