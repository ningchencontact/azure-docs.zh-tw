---
title: 在適用於 MySQL 的 Azure 資料庫中設定服務參數
description: 本文說明如何使用 Azure CLI 命令列公用程式，在適用於 MySQL 的 Azure 資料庫中設定服務參數。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 4c04cb77513ec070edce739aa0a49447dc915a1b
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265209"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>使用 Azure CLI 自訂伺服器設定參數
您可以使用 Azure CLI (Azure 命令列公用程式)，來列出、顯示和更新適用於 MySQL 的 Azure 資料庫伺服器的設定參數。 有一部分的引擎設定會在伺服器層級公開而且可供修改。 

## <a name="prerequisites"></a>先決條件
若要逐步執行本作法指南，您需要︰
- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) 命令列公用程式，或在瀏覽器中使用 Azure Cloud Shell。

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>列出適用於 MySQL 的 Azure 資料庫伺服器的伺服器設定參數
若要列出伺服器中所有可修改的參數及其值，請執行 [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) 命令。

您可以針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mysql.database.azure.com**，列出伺服器組態參數。
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
如需每個列出參數的定義，請參閱[伺服器系統變數](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html) \(英文\) 中的 MySQL 參考小節。

## <a name="show-server-configuration-parameter-details"></a>顯示伺服器設定參數的詳細資料
若要顯示有關伺服器特定設定參數的詳細資訊，請執行 [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) 命令。

此範例會針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mysql.database.azure.com**，顯示 **slow\_query\_log** 伺服器組態參數的詳細資料。
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>修改伺服器設定參數值
您也可以修改特定伺服器設定參數的值，以更新適用於 MySQL 伺服器引擎的基礎設定值。 若要更新設定，請使用 [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) 命令。 

若要針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mysql.database.azure.com** 更新 **slow\_query\_log** 伺服器組態參數。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
如果您想要重設設定參數的值，請省略選擇性的 `--value` 參數，而服務會套用預設值。 針對上述範例，看起來應該像這樣：
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
此程式碼會將 **slow\_query\_log** 設定重設為預設值 **OFF**。 

## <a name="next-steps"></a>後續步驟
- 如何設定 [Azure 入口網站中的伺服器參數](howto-server-parameters.md)