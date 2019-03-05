---
title: Azure CLI 指令碼範例 - 從 Azure 應用程式設定存放區匯出 | Microsoft Docs
description: 提供從 Azure 應用程式設定存放區匯出的相關資訊和範例指令碼
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3310dc5d72284e8d94b95b855fee90d560205fa4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884384"
---
# <a name="export-from-an-azure-app-configuration-store"></a>從 Azure 應用程式設定存放區匯出

此範例指令碼會從 Azure 應用程式設定存放區匯出索引鍵/值。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須先執行下列命令，以安裝 Azure 應用程式設定 CLI 擴充功能：

        az extension add -n appconfig

## <a name="sample-script"></a>範例指令碼

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令從應用程式設定存放區匯出。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az appconfig export](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-export) | 從應用程式設定存放區資源匯出。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure 應用程式設定文件](../cli-samples.md)中找到其他的應用程式設定 CLI 指令碼範例。
