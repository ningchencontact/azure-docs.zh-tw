---
title: Azure CLI 指令碼範例 - 取得 Azure Cache for Redis 的主機名稱、連接埠和金鑰 | Microsoft Docs
description: Azure CLI 指令碼範例 - 取得 Azure Cache for Redis 執行個體的主機名稱、連接埠和金鑰
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fd69c3182f026996618fea5e26466e8434ea36f6
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754450"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>取得 Azure Cache for Redis 的主機名稱、連接埠和金鑰

在此案例中，您會了解如何擷取主機名稱、連接埠和金鑰，用以連線到 Azure Cache for Redis 執行個體。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令擷取 Azure Cache for Redis 執行個體的主機名稱、金鑰和連接埠。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | 取得 Azure Cache for Redis 執行個體的詳細資料。 |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | 取得 Azure Cache for Redis 執行個體的存取金鑰。 |


## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Cache for Redis 文件](../cli-samples.md)中找到其他 Azure Cache for Redis CLI 指令碼範例。
