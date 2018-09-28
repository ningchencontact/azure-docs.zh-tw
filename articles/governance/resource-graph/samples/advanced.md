---
title: 進階 Azure Resource Graph 查詢
description: 使用 Azure Resource Graph 執行一些進階查詢。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 6798ef3f81ff8e733886fccc56f6474afc2ee5e9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992485"
---
# <a name="advanced-resource-graph-queries"></a>進階 Resource Graph 查詢

透過 Azure Resource Graph 了解查詢的第一個步驟是對[查詢語言](../concepts/query-language.md)進行基本的認識。 如果您還不熟悉 Kusto Query Language (KQL)，建議檢閱基本概念，以了解如何撰寫所需資源的要求。

我們將逐步解說下列進階查詢︰

> [!div class="checklist"]
> - [尋找無磁碟加密或未啟用加密的虛擬機器擴展集](#vmss-not-encrypted)
> - [列出所有標記名稱](#list-all-tags)

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free)。

## <a name="language-support"></a>語言支援

Azure CLI (透過擴充功能) 與 Azure PowerShell (透過模組) 支援 Azure Resource Graph。 在執行下列任何查詢之前，請檢查您的環境已準備就緒。 請參閱 [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) 與 [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module)，以了解安裝及驗證所選殼層環境的步驟。

## <a name="vmss-not-encrypted"></a>尋找無磁碟加密或未啟用加密的虛擬機器擴展集

此查詢會尋找虛擬機器擴展集資源、展開擴充功能詳細資料、查看擴充功能**類型**和 **EncryptionOperation** 屬性、提供摘要說明，並傳回預期設定計數是 0 的資源。

```Query
where type =~ 'microsoft.compute/virtualmachinescalesets'
| project id, extension = properties.virtualMachineProfile.extensionProfile.extensions
| where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"'
| project id
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachinescalesets' | project id, extension = properties.virtualMachineProfile.extensionProfile.extensions | where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"' | project id"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachinescalesets' | project id, extension = properties.virtualMachineProfile.extensionProfile.extensions | where extension !has 'AzureDiskEncryption' or extension !has '"EnableEncryption"' | project id"
```

## <a name="list-all-tags"></a>列出所有標記名稱

此查詢的開頭為標記，而且會建置 JSON 物件，列出所有的唯一標記名稱及其對應的類型。

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```azurepowershell-interactive
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](starter.md)的範例
- 深入了解[查詢語言](../concepts/query-language.md)
- 了解[探索資源](../concepts/explore-resources.md)