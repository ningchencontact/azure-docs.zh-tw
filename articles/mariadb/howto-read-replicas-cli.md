---
title: 在適用於 MariaDB 的 Azure 資料庫中建立及管理讀取複本
description: 本文說明如何使用 Azure CLI 設定和管理適用於 MariaDB 的 Azure 資料庫中的讀取複本。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 7944f985f2317690f3a13add783192c49acbe22f
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907651"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli"></a>如何使用 Azure CLI 在適用於 MariaDB 的 Azure 資料庫中建立及管理讀取複本

在本文中, 您將瞭解如何使用 Azure CLI, 在與適用於 MariaDB 的 Azure 資料庫服務中的主伺服器相同的 Azure 區域內建立及管理讀取複本。

> [!IMPORTANT]
> 您可以在與主伺服器相同的區域中, 或在您選擇的任何其他 Azure 區域中建立讀取複本。 讀取複本 (相同區域和跨區域) 目前為公開預覽狀態。

## <a name="prerequisites"></a>必要條件

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- 將做為主伺服器使用的[適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-portal.md)。 

> [!IMPORTANT]
> 「讀取複本」功能僅適用于一般用途或記憶體優化定價層中的適用於 MariaDB 的 Azure 資料庫伺服器。 請確定主要伺服器處於這些定價層中。

## <a name="create-a-read-replica"></a>建立讀取複本

使用下列命令可以建立讀取複本伺服器︰

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica create` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| name | mydemoreplicaserver | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複寫的現有主要伺服器的名稱或識別碼。 |

若要建立跨區域讀取複本, 請使用`--location`參數。 下列 CLI 範例會在美國西部建立複本。

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本, 請造訪[讀取複本概念一文](concepts-read-replicas.md)。 

> [!NOTE]
> 系統會以與主要伺服器相同的伺服器設定建立讀取複本。 複本伺服器設定在建立後可以變更。 建議複本伺服器設定的值應保持等於或大於主要伺服器，以確保複本伺服器能保持與主要伺服器一致。

## <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器

> [!IMPORTANT]
> 停止複寫至伺服器是無法復原的。 一旦停止主要與複本伺服器之間進行複寫，便無法復原。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。 此伺服器無法再次設定為複本伺服器。

使用下列命令，可以停止複寫至讀取複本伺服器︰

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mariadb server replica stop` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器所在的資源群組。  |
| name | mydemoreplicaserver | 要停止複寫的複本伺服器名稱。 |

## <a name="delete-a-replica-server"></a>刪除複本伺服器

若要刪除讀取複本伺服器, 可以執行 **[az 適用于 mariadb server delete](/cli/azure/mariadb/server)** 命令。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>刪除主要複本

> [!IMPORTANT]
> 刪除主要伺服器會停止對所有複本伺服器複寫，並刪除主要伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要刪除主伺服器, 您可以執行 **[az 適用于 mariadb server delete](/cli/azure/mariadb/server)** 命令。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>列出主要伺服器的複本

若要檢視指定主要伺服器的所有複本，請執行下列命令： 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica list` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| server-name | mydemoserver | 主要伺服器的名稱或識別碼。 |

## <a name="next-steps"></a>後續步驟

- 深入了解[讀取複本](concepts-read-replicas.md)
