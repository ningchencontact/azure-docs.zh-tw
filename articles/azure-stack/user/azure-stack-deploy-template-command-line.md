---
title: 以命令列部署 Azure Stack 中的範本 | Microsoft Docs
description: 了解如何使用跨平台命令列介面 (CLI) 部署範本到 Azure Stack 上。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: d40f9e8bf319f327879a71b23f9d9b82da924c2f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365429"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>使用命令列部署 Azure Stack 中的範本

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

使用命令列在 Azure Stack 開發套件環境中部署 Azure Resource Manager 範本。 Azure Resource Manager 範本可藉由單一協調作業，來部署和佈建應用程式的所有資源。

## <a name="before-you-begin"></a>開始之前

 - 使用 Azure CLI [安裝並連線](azure-stack-version-profiles-azurecli2.md)至 Azure Stack。
 - 從 [建立儲存體帳戶範例範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account) 下載 *azuredeploy.json* 和 *azuredeploy.parameters.json* 檔案。
 
## <a name="deploy-template"></a>部署範本

瀏覽至下載這些檔案的資料夾，並執行下列命令來部署範本：

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

此命令會將範本部署至 Azure Stack POC 預設位置中的資源群組 **cliRG**。

## <a name="validate-template-deployment"></a>驗證範本部署

若要查看此資源群組和儲存體帳戶，請使用下列命令：

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>後續步驟

若要深入了解部署範本，請參閱：

[使用 PowerShell 部署範本](azure-stack-deploy-template-powershell.md)

