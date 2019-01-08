---
title: Azure PowerShell 指令碼範例 - 將應用程式連線至儲存體帳戶 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 將 App Service 應用程式連線至儲存體帳戶
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 851d8e0c8d7e7a746af2f364ab986f8e5f679a84
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650519"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>將 App Service 應用程式連線至儲存體帳戶

在此案例中，您將了解如何建立 Azure 儲存體帳戶和 App Service 應用程式。 然後，您會使用應用程式設定來將儲存體帳戶連結到該應用程式。

您可以視需要使用 [Azure PowerShell 指南](/powershell/azure/overview) \(英文\) 中的指示來安裝 Azure PowerShell，然後執行 `Connect-AzureRmAccount` 來建立與 Azure 的連線。

## <a name="sample-script"></a>範例指令碼

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect an app to a storage account")]

## <a name="clean-up-deployment"></a>清除部署 

執行指令碼範例之後，您可以使用下列命令來移除資源群組、App Service 應用程式及所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | 建立 App Service 方案。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | 建立 App Service 應用程式。 |
| [New-AzureRMStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | 建立儲存體帳戶。 |
| [Get-AzureRMStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | 取得 Azure 儲存體帳戶的存取金鑰。 |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | 修改 App Service 應用程式的設定。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure PowerShell 範例](../samples-powershell.md)中找到適用於 Azure App Service 的其他 Azure PowerShell 範例。
