---
title: Azure CLI 指令碼 - 調整適用於 PostgreSQL 的 Azure 資料庫
description: Azure CLI 指令碼範例 - 在查詢計量之後，將「適用於 PostgreSQL 的 Azure 資料庫」伺服器調整為不同的效能等級。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 04/05/2018
ms.openlocfilehash: 65958eb1e2d7cc4492e0437b24db77f00ebc7eba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "66154517"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>使用 Azure CLI 監視和調整單一 PostgreSQL 伺服器
此範例 CLI 指令碼會在查詢計量之後，將單一「適用於 PostgreSQL 的 Azure 資料庫」伺服器調整為不同的效能等級。 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本執行 CLI，本文需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以檢查版本。 請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli) 以安裝或升級 Azure CLI 版本。

## <a name="sample-script"></a>範例指令碼
在此範例指令碼中，請編輯醒目提示的命令列，將系統管理員使用者名稱和密碼更新為您自己的使用者名稱和密碼。 請以您自己的訂用帳戶識別碼取代 `az monitor` 命令中使用的訂用帳戶識別碼。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>清除部署
執行指令碼之後，請使用下列命令來移除資源群組及其相關聯的所有資源。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>指令碼說明
此指令碼會使用下表中簡述的命令：

| **命令** | **注意事項** |
|---|---|
| [az group create](/cli/azure/group) | 建立用來存放所有資源的資源群組。 |
| [az postgres server create](/cli/azure/postgres/server) | 建立主控資料庫的 PostgreSQL 伺服器。 |
| [az monitor metrics list](/cli/azure/monitor/metrics) | 列出資源的度量值。 |
| [az group delete](/cli/azure/group) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
- 閱讀更多有關 Azure CLI 的資訊：[Azure CLI 文件](/cli/azure)
- 嘗試額外的指令碼：[Azure Database for PostgreSQL 的 Azuer CLI 範例](../sample-scripts-azure-cli.md)
- 閱讀更多有關調整規模的資訊：[服務層](../concepts-service-tiers.md)及[計算單位和儲存單位](../concepts-compute-unit-and-storage.md)
