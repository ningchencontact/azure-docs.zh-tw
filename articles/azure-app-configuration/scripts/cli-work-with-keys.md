---
title: Azure CLI 指令碼範例 - 在 Azure 應用程式設定存放區中使用索引鍵/值 | Microsoft Docs
description: 提供在 Azure 應用程式設定存放區中搭配使用索引鍵/值的資訊
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
ms.openlocfilehash: 9288ea08da6335dd29e7a15a9bc871b76c1ce7e9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438426"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>在 Azure 應用程式設定存放區中搭配使用索引鍵/值

此範例指令碼會在 Azure 應用程式設定存放區中建立新的索引鍵/值、列出所有現有的索引鍵/值、更新新建立索引鍵的值，以及在最後刪除該值。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

您必須先執行下列命令，以安裝 Azure 應用程式設定 CLI 擴充功能：

        az extension add -n appconfig

## <a name="sample-script"></a>範例指令碼

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，在應用程式設定存放區中的索引鍵/值上進行作業。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig) | 建立或更新索引鍵/值。 |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig) | 列出應用程式設定存放區中的索引鍵/值。 |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig) | 刪除索引鍵/值。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure 應用程式設定文件](../cli-samples.md)中找到其他的應用程式設定 CLI 指令碼範例。
