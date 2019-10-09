---
title: 使用 Azure CLI 在適用於 MariaDB 的 Azure 資料庫中存取伺服器記錄
description: 本文說明如何使用 Azure CLI 命令列公用程式，在適用於 MariaDB 的 Azure 資料庫中存取伺服器記錄。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: ffc724ef5133ee25643a966d2b6d8448a4c3a920
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023606"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>使用 Azure CLI 設定和存取伺服器記錄
您可以使用 Azure CLI (Azure 的命令列公用程式) 來下載適用於 MariaDB 的 Azure 資料庫的伺服器記錄。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 MariaDB 的 Azure 資料庫伺服器](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 或瀏覽器中的 Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mariadb"></a>設定適用於 MariaDB 的 Azure 資料庫的記錄
您可以採取下列步驟，設定伺服器以存取 MariaDB 慢速查詢記錄檔：
1. 藉由將 **slow\_query\_log** 參數設為 ON 來開啟記錄功能。
2. 調整其他參數，例如 **long\_query\_time** 和 **log\_slow\_admin\_statements**。

若要了解如何透過 Azure CLI 設定這些參數的值，請參閱[如何設定伺服器參數](howto-configure-server-parameters-cli.md)。

例如，下列 CLI 命令會開啟慢速查詢記錄檔、將長時間查詢的時間設定為 10 秒，並且會關閉慢速管理陳述式的記錄。 最後，它會列出設定選項供您檢閱。
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>列出適用於 MariaDB 的 Azure 資料庫伺服器之記錄
若要列出伺服器的可用緩慢查詢記錄檔，請執行[az 適用于 mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)命令。

您可以針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mariadb.database.azure.com** 列出記錄檔。 然後將記錄檔的清單導向名為 **log\_files\_list.txt** 的文字檔。
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>從伺服器下載記錄
使用 [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) 命令，即可下載您伺服器適用的個別記錄。

使用下列範例，針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mariadb.database.azure.com**，將特定的記錄檔下載至您的本機環境。
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>後續步驟
- 深入瞭解[適用於 MariaDB 的 Azure 資料庫中的緩慢查詢記錄](concepts-server-logs.md)。
