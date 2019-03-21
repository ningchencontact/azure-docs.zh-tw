---
title: Azure CLI 指令碼範例 - 匯入到 Azure 應用程式設定存放區 | Microsoft Docs
description: 提供匯入到 Azure 應用程式設定存放區的相關資訊和範例指令碼
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
ms.openlocfilehash: 0df8e19d3c6f0680f1eb1b0157c3bee5c9841e4c
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575081"
---
# <a name="import-to-an-azure-app-configuration-store"></a>匯入到 Azure 應用程式設定存放區

此範例指令碼會將索引鍵/值匯入 Azure 應用程式設定存放區。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須先執行下列命令，以安裝 Azure 應用程式設定 CLI 擴充功能：

        az extension add -n appconfig

## <a name="sample-script"></a>範例指令碼

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --file ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來匯入應用程式設定存放區。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az appconfig import](/cli/azure/ext/appconfig/appconfig) | 匯入到應用程式設定存放區資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure 應用程式設定文件](../cli-samples.md)中找到其他的應用程式設定 CLI 指令碼範例。
