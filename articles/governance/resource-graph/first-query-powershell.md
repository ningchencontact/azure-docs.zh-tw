---
title: 使用 Azure PowerShell 執行您的第一個 Resource Graph 查詢
description: 本文將引導您完成為 Azure PowerShell 啟用 Resource Graph 模組並執行第一個查詢的步驟。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 7a706c65fbdd64103854b02e891c96cbf927f8a1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962533"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>使用 Azure PowerShell 執行您的第一個 Resource Graph 查詢

使用 Azure Resource Graph 的第一個步驟是確定已安裝 Azure PowerShell 的模組。 本快速入門逐步引導您完成將模組新增至您的 Azure PowerShell 安裝的程序。 您可以將模組與在本機安裝或透過 [Azure Cloud Shell](https://shell.azure.com) 安裝之 Azure PowerShell 搭配使用。

在此過程結束時，模組將新增至所選的 Azure PowerShell 安裝中，並執行您的第一個 Resource Graph 查詢。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="add-the-resource-graph-module"></a>新增 Resource Graph 模組

若要啟用 Azure PowerShell 來查詢 Azure Resource Graph，必須新增模組。 此模組適用可使用 Azure PowerShell 的任何地方，包括 [Cloud Shell](https://shell.azure.com) (獨立與內部入口網站)、[Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)，或在本機安裝。

1. 確定已安裝 Azure PowerShell 6.3.0 或更高版本。 如果未安裝，請依照[這些步驟](/powershell/azure/install-azurerm-ps)操作。

1. 確定已安裝 PowerShellGet。 如果未安裝或更新，請依照[這些步驟](/powershell/gallery/installing-psget)操作。

1. 從**系統管理** PowerShell 提示字元中，執行下列命令：

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module AzureRm.ResourceGraph
   ```

1. 驗證已匯入模組，而且是正確的版本 (0.1.0)：

   ```azurepowershell-interactive
   # Get a list of commands for the imported AzureRm.Graph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>執行第一個 Resource Graph 查詢

現在，Azure PowerShell 模組已新增至您選擇的環境，現在可以試試看簡單的 Resource Graph 查詢。 查詢會傳回前五個 Azure 資源支每個資源的**名稱**與**資源類型**。

1. 使用 `Search-AzureRmGraph` Cmdlet 執行第一個 Azure Resource Graph 查詢：

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > 當此查詢範例未提供排序修飾詞，例如 `order by`，多次執行此查詢可能會為每個要求產生不同的資源集。

1. 將查詢更新為 `order by` **名稱**屬性：

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > 如同第一個查詢一樣，多次執行此查詢可能會為每個要求產生不同的資源集。 查詢命令的順序很重要。 在此範例中，`order by` 會出現在 `limit` 之後。 這會先限制查詢結果，然後將結果進行排序。

1. 將查詢更新為第一個 `order by`，**名稱**屬性，然後 `limit` 為前 5 個結果：

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

多次執行最終查詢時，假設您的環境中未變更任何內容，傳回的結果將會一致且符合預期 - 依照**名稱**屬性排序，但仍限制為前 5 個結果。

## <a name="clean-up"></a>清除

如果您想要從 Azure PowerShell 環境中移除 Resource Graph 模組，則可以使用下列命令：

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> 這不會刪除先前下載的模組檔案。 它只會從正在執行的 PowerShell 工作階段中移除。

## <a name="next-steps"></a>後續步驟

- 了解關於[查詢語言](./concepts/query-language.md)的詳細資訊
- 了解[探索資源](./concepts/explore-resources.md)
- 使用 [Azure CLI](first-query-azurecli.md) 執行第一個查詢
- 請參閱[入門查詢](./samples/starter.md)的範例
- 請參閱[進階查詢](./samples/advanced.md)的範例