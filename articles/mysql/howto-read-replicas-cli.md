---
title: 在適用於 MySQL 的 Azure 資料庫中建立與管理讀取複本
description: 本文說明如何使用 Azure CLI 在適用於 MySQL 的 Azure 資料庫中設定與管理讀取複本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 8a9366005ceef5a99fd66f0ec0cd53c98b481ee1
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309323"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>如何使用 Azure CLI 在適用於 MySQL 的 Azure 資料庫中建立與管理讀取複本

在本文中，您將了解如何使用 Azure CLI 在「適用於 MySQL 的 Azure 資料庫」服務中，於與主要伺服器相同的 Azure 區域內建立與管理讀取複本。

## <a name="prerequisites"></a>必要條件

- [安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-portal.md)，將作為主要伺服器。 

> [!IMPORTANT]
> 讀取複本功能僅供「適用於 MySQL 的 Azure 資料庫」伺服器用於一般用途或記憶體最佳化定價層。 請確定主要伺服器處於這些定價層中。

## <a name="create-a-read-replica"></a>建立讀取複本

使用下列命令可以建立讀取複本伺服器︰

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| name | mydemoreplicaserver | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 要從中複寫的現有主要伺服器的名稱或識別碼。 |

若要建立跨區域讀取複本，請使用`--location`參數。 下列 CLI 範例會在美國西部建立複本。

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要深入瞭解您可以在哪些區域中建立複本，請造訪[讀取複本概念一文](concepts-read-replicas.md)。 

> [!NOTE]
> 系統會以與主要伺服器相同的伺服器設定建立讀取複本。 複本伺服器設定在建立後可以變更。 建議複本伺服器設定的值應保持等於或大於主要伺服器，以確保複本伺服器能保持與主要伺服器一致。

## <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器

> [!IMPORTANT]
> 停止複寫至伺服器是無法復原的。 一旦停止主要與複本伺服器之間進行複寫，便無法復原。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。 此伺服器無法再次設定為複本伺服器。

使用下列命令，可以停止複寫至讀取複本伺服器︰

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器所在的資源群組。  |
| name | mydemoreplicaserver | 要停止複寫的複本伺服器名稱。 |

## <a name="delete-a-replica-server"></a>刪除複本伺服器

執行 **[az mysql server delete](/cli/azure/mysql/server)** 命令，即可刪除讀取複本伺服器。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>刪除主要複本

> [!IMPORTANT]
> 刪除主要伺服器會停止對所有複本伺服器複寫，並刪除主要伺服器本身。 複本伺服器會變成獨立伺服器，進而支援讀取和寫入。

若要刪除主要伺服器，您可以執行 **[az mysql server delete](/cli/azure/mysql/server)** 命令。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>列出主要伺服器的複本

若要檢視指定主要伺服器的所有複本，請執行下列命令： 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list` 命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  複本伺服器會建立於其中的資源群組。  |
| server-name | mydemoserver | 主要伺服器的名稱或識別碼。 |

## <a name="next-steps"></a>後續步驟

- 深入了解[讀取複本](concepts-read-replicas.md)
