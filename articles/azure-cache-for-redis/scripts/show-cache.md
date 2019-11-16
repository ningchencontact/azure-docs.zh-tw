---
title: Azure CLI 指令碼範例 - 取得 Azure Cache for Redis 的詳細資料
description: Azure CLI 指令碼範例 - 取得 Azure Cache for Redis 的詳細資料
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: f3e6c6dab95722eebdc4a175379444ef5840cad1
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122478"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>取得 Azure Cache for Redis 的詳細資料

在此案例中，您將了解關於如何擷取 Azure Cache for Redis 執行個體 (包括其佈建狀態) 的詳細資料。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，擷取 Azure Cache for Redis 執行個體的詳細資料。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | 取得 Azure Cache for Redis 執行個體的詳細資料。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Cache for Redis 文件](../cli-samples.md)中找到其他 Azure Cache for Redis CLI 指令碼範例。
