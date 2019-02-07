---
title: Azure CLI 指令碼 - 將適用於 MySQL 的 Azure 資料庫伺服器還原至先前的時間點
description: 此範例 CLI 指令碼會將適用於 MySQL 的 Azure 資料庫伺服器還原至先前的時間點。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 38825f7815713678946e86252d4c8a1bee0abbc6
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752682"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 還原「適用於 MySQL 的 Azure 資料庫」伺服器
此範例 CLI 指令碼會將單一「適用於 MySQL 的 Azure 資料庫」伺服器還原至先前的時間點。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝並使用 CLI，此範例會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>範例指令碼
在此範例指令碼中，請變更醒目提示的命令列以自訂系統管理員使用者名稱和密碼。 請以您自己的訂用帳戶識別碼取代用於 az monitor 命令中的訂用帳戶識別碼。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>清除部署
在執行過指令碼範例之後，您可以使用下列命令來移除資源群組和所有與其相關聯的資源。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| **命令** | **注意事項** |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az mysql server create](/cli/azure/mysql/server) | 建立主控資料庫的 MySQL 伺服器。 |
| [az mysql server restore](/cli/azure/mysql/server) | 從備份還原伺服器。 |
| [az group delete](/cli/azure/group) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 閱讀更多有關 Azure CLI 的資訊：[Azure CLI 文件](/cli/azure)。
- 嘗試額外的指令碼：[Azure Database for MySQL 的 Azuer CLI 範例](../sample-scripts-azure-cli.md)
