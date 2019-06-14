---
title: 包含檔案
description: 包含檔案
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e3337d0a0159ef5e57ea3bbaba1c6cc2ac8489ff
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808135"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>使用 Azure CLI 來擷取主機名稱、連接埠及存取金鑰

若要擷取的主機名稱和連接埠使用 Azure CLI，您可以呼叫[az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show)，並擷取的金鑰，您可以呼叫[az redis 清單索引鍵](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys)。 下列指令碼會呼叫這兩個命令，並將主機名稱、 連接埠和金鑰，以在主控台的回應。

```azurecli
# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

如需有關此指令碼的詳細資訊，請參閱 <<c0> [ 主機名稱、 連接埠和金鑰，取得適用於 Redis Azure 快取](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md)。 如需 Azure CLI 的詳細資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)並[開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。
