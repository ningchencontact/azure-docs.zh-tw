---
title: 快速入門：建立適用於 MariaDB 的 Azure 資料庫伺服器 - Azure CLI
description: 本快速入門說明如何使用 Azure CLI，在 Azure 資源群組中建立適用於 MariaDB 的 Azure 資料庫伺服器。
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 9e87dacb80aa7fc5f5b073e631fc06ae74b3ad00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996599"
---
# <a name="create-an-azure-database-for-mariadb-server-using-azure-cli"></a>使用 Azure CLI 建立適用於 MariaDB 的 Azure 資料庫伺服器
本快速入門說明如何使用 Azure CLI，大約 5 分鐘內，在 Azure 資源群組中建立適用於 MariaDB 的 Azure 資料庫伺服器。 Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果您有多個訂用帳戶，請選擇資源所在或作為計費對象的適當訂用帳戶。 使用 [az account set](/cli/azure/account#az-account-set) 命令來選取您帳戶底下的特定訂用帳戶 ID。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組
使用 [az group create](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 命令建立 [Azure 資源群組](/cli/azure/group#az-group-create)。 資源群組是在其中以群組方式部署與管理 Azure 資源的邏輯容器。

下列範例會在 `westus` 位置建立名為 `myresourcegroup` 的資源群組。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>建立適用於 MariaDB 的 Azure 資料庫伺服器
使用 **[az mysql server create](/cli/azure/mariadb/server#az-mariadb-server-create)** 命令來建立適用於 MariaDB 的 Azure 資料庫伺服器。 一部伺服器可以管理多個資料庫。 一般而言，每個專案或每個使用者會使用個別的資料庫。

**設定** | **範例值** | **說明**
---|---|---
name | mydemoserver | 選擇可識別適用於 MariaDB 的 Azure 資料庫伺服器的唯一名稱。 伺服器名稱只能包含小寫字母、數字及連字號 (-) 字元。 此名稱必須包含 3 到 63 個字元。
resource-group | myresourcegroup | 提供 Azure 資源群組的名稱。
sku-name | GP_Gen5_2 | SKU 的名稱。 遵循簡短形式的慣例 {pricing tier}_{compute generation}_{vCores}。 請參閱下表，以取得更多有關 sku-name 參數的詳細資訊。
backup-retention | 7 | 備份應保留的時間長度。 單位為天。 範圍為 7-35 天。 
geo-redundant-backup | 已停用 | 是否應針對此伺服器啟用異地備援備份。 允許的值：Enabled、Disabled。
location | westus | 伺服器的 Azure 位置。
ssl-enforcement | 已啟用 | 是否應針對此伺服器啟用 SSL。 允許的值：Enabled、Disabled。
storage-size | 51200 | 伺服器的儲存體容量 (單位為 MB)。 有效 storage-size 的最小值為 5120 MB，並以 1024 MB 的增量方式增加。 如需儲存體大小限制的詳細資訊，請參閱[定價層](./concepts-pricing-tiers.md)文件。 
version | 10.2 | MariaDB 主要引擎版本。
admin-user | myadmin | 適用於系統管理員登入的使用者名稱。 此名稱不得為 **azure_superuser**、**admin**、**administrator**、**root**、**guest** 或 **public**。
admin-password | Password123 | 系統管理員使用者的密碼。 其必須包含 8 到 128 個字元。 您的密碼必須包含下列三個類別的字元：英文大寫字母、英文小寫字母、數字和非英數字元。

sku-name 參數值會遵循慣例 {pricing tier}\_{compute generation}\_{vCores}，如下列範例所示：
+ `--sku-name B_Gen5_4` 對應於基本、第 5 代和 4 個 vCore。
+ `--sku-name GP_Gen5_32` 對應於一般用途、第 5 代和 32 個 vCore。
+ `--sku-name MO_Gen5_2` 對應於記憶體最佳化、第 5 代和 2 個 vCore。

請參閱[定價層](./concepts-pricing-tiers.md)文件，以了解每個區域和每一層的有效值。

下列範例會使用伺服器管理員登入 `myadmin` 在 `myresourcegroup` 資源群組中建立名為 `mydemoserver` 的伺服器 (位於美國西部)。 這是**第 5 代****一般用途**伺服器，其具有 2 個 **vCore**。 伺服器的名稱會對應至 DNS 名稱，因此必須是 Azure 中全域唯一的。 將 `<server_admin_password>` 替換成您自己的值。
```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-firewall-rule"></a>設定防火牆規則
使用 **[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create)** 命令，來建立適用於 MariaDB 的 Azure 資料庫伺服器層級的防火牆規則。 伺服器層級的防火牆規則允許外部應用程式 (例如 **mysql** 命令列工具或 MySQL Workbench) 透過 Azure MariaDB 服務防火牆連線到您的伺服器。 

下列範例會建立名為 `AllowMyIP` 的防火牆規則，以允許來自特定 IP 位址 192.168.0.1 的連線。 請替換為與您的連線來源相對應的 IP 位址或 IP 位址範圍。 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> 適用於 MariaDB 的 Azure 資料庫連線會透過連接埠 3306 進行通訊。 如果您嘗試從公司網路內進行連線，可能不允許透過連接埠 3306 的輸出流量。 若是如此，除非 IT 部門開啟連接埠 3306，否則您無法連線到您的伺服器。
> 

## <a name="configure-ssl-settings"></a>進行 SSL 設定
預設會強制執行您的伺服器與用戶端應用程式之間的 SSL 連線。 此預設值可藉由加密透過網際網路的資料流，確保「移動中」資料的安全性。 為了讓本快速入門更容易進行，請停用您伺服器的 SSL 連線。 不建議對生產伺服器停用 SSL。 如需詳細資訊，請參閱[在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MariaDB 的 Azure 資料庫](./howto-configure-ssl.md)。

下列範例會停用在 MariaDB 伺服器上強制執行 SSL。
 
 ```azurecli-interactive
 az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>取得連線資訊

若要連線到您的伺服器，您必須提供主機資訊和存取認證。

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

結果會採用 JSON 格式。 請記下 **fullyQualifiedDomainName** 和 **administratorLogin**。
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-server-using-mysql-command-line"></a>使用 mysql 命令列連線到伺服器
使用 **mysql** 命令列工具連線到伺服器。 您可以從[這裡](https://dev.mysql.com/downloads/)下載此命令列工具，並將它安裝於電腦上。 相反地，您也可以按一下程式碼範例上的 [試用] 按鈕，或是 Azure 入口網站右上角工具列的 `>_` 按鈕，並啟動 **Azure Cloud Shell**。

輸入下一個命令： 

1. 使用 **mysql** 命令列工具連線到伺服器︰
```azurecli-interactive
 mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

2. 在 mysql > 提示字元上檢視伺服器狀態：
```sql
status
```
如果一切順利，命令列工具應輸出下列文字︰

```bash
C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.39.0 MariaDB Server

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

Connection id:          64681
Current database:
Current user:           myadmin@40.118.201.21
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MariaDB Server
Protocol version:       10
Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 1 day 3 hours 28 min 50 sec

Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
--------------

mysql>
```

> [!TIP]
> 如需其他命令，請參閱 [MySQL 5.7 參考手冊 - 第 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)。

## <a name="connect-to-server-using-mysql-workbench"></a>使用 MySQL Workbench 連線到伺服器
1.  在您的用戶端電腦上啟動 MySQL Workbench 應用程式。 您可以從[這裡](https://dev.mysql.com/downloads/workbench/)下載並安裝 MySQL Workbench。

2.  在 [設定新連線] 對話方塊的 [參數] 索引標籤上輸入下列資訊︰

   ![設定新連線](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

| **設定** | **建議的值** | **說明** |
|---|---|---|
|   連線名稱 | 我的連線 | 指定此連線的標籤 (這可以是任何項目) |
| 連線方式 | 選擇標準 (TCP/IP) | 使用 TCP/IP 通訊協定連線到適用於 MariaDB 的 Azure 資料庫 |
| 主機名稱 | mydemoserver.mariadb.database.azure.com | 您先前記下的伺服器名稱。 |
| Port | 3306 | 會使用 MariaDB 的預設連接埠。 |
| 使用者名稱 | myadmin@mydemoserver | 先前記下的伺服器管理員登入。 |
| 密碼 | **** | 使用您稍早設定的管理帳戶密碼。 |

按一下 [測試連線] 以測試所有參數是否都已設定正確。
您現在可以按一下連線，以成功連線到伺服器。

## <a name="clean-up-resources"></a>清除資源
如果您不需要這些資源來進行其他快速入門/教學課程，可以執行下列命令加以刪除︰ 

```azurecli-interactive
az group delete --name myresourcegroup
```

若您只想要刪除新建立的伺服器，則可執行 **[az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete)** 命令。
```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

<!--
## Next steps

> [!div class="nextstepaction"]
> [Design a MariaDB Database with Azure CLI](./tutorial-design-database-using-cli.md)
-->