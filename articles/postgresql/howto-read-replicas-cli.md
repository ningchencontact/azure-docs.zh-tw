---
title: 從 Azure CLI 管理適用於 PostgreSQL 的 Azure 資料庫單一伺服器的讀取複本
description: 瞭解如何從 Azure CLI 管理適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的讀取複本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 63a8acad3c393a4c4d9c6a3b6750f1f934dad43d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907432"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>從 Azure CLI 建立及管理讀取複本

在本文中, 您將瞭解如何從 Azure CLI 建立和管理適用於 PostgreSQL 的 Azure 資料庫中的讀取複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

> [!IMPORTANT]
> 您可以在與主伺服器相同的區域中, 或在您選擇的任何其他 Azure 區域中建立讀取複本。 跨區域複寫目前為公開預覽狀態。

## <a name="prerequisites"></a>必要條件
- 使用[適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)作為主要伺服器。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 


## <a name="prepare-the-master-server"></a>準備主要伺服器
若要在一般用途和記憶體最佳化層中準備主要伺服器，這些是必要步驟。

`azure.replication_support` 參數必須在主要伺服器上設定為 **REPLICA**。 當此靜態參數變更時, 需要重新開機伺服器, 變更才會生效。

1. 設定`azure.replication_support`為 [複本]。

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. 重新開機以將變更套用至伺服器。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>建立讀取複本

[Az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create)命令需要下列參數:

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group | myresourcegroup |  將在其中建立複本伺服器的資源群組。  |
| name | mydemoserver-replica | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複寫之現有主伺服器的名稱或資源識別碼。 |

在下列 CLI 範例中, 會在與主伺服器相同的區域中建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

若要建立跨區域讀取複本, 請使用`--location`參數。 下列 CLI 範例會在美國西部建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本, 請造訪[讀取複本概念一文](concepts-read-replicas.md)。 

如果您尚未在一般用途`azure.replication_support`或記憶體優化的主伺服器上, 將參數設定為 [**複本**], 並重新啟動伺服器, 就會收到錯誤。 建立複本之前, 請先完成這兩個步驟。

使用與主伺服器相同的計算和儲存設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 將主伺服器設定更新為新值之前, 請將複本設定更新為相等或更大的值。 此動作可協助複本跟上對主伺服器所做的任何變更。

## <a name="list-replicas"></a>列出複本
您可以使用[az postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)命令來查看主伺服器的複本清單。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用[az postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)命令, 停止主伺服器和讀取複本之間的複寫。

停止主要伺服器和讀取複本的複寫之後，即無法再復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>刪除主要或複本伺服器
若要刪除主要或複本伺服器, 請使用[az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)命令。

刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>後續步驟
* 深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。
* 了解如何[在 Azure 入口網站中建立及管理讀取複本](howto-read-replicas-portal.md)。