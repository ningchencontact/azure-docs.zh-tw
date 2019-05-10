---
title: 適用於 Azure Cosmos DB 的 Azure PowerShell 範例
description: Azure PowerShell 範例：協助您建立和管理 Azure Cosmos DB 帳戶的指令碼。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069292"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>適用於 Azure Cosmos DB 的 Azure PowerShell 範例

下表包含適用於 Azure Cosmos DB for Core (SQL) API 的範例 Azure PowerShell 指令碼的連結。

| |  |
|---|---|
|**Azure Cosmos 帳戶**||
|[建立帳戶](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos SQL API 帳戶。 |
|[取得帳戶](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得 Azure Cosmos 帳戶的屬性。 |
|[新增區域](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得 Azure Cosmos 帳戶，並將區域新增至位置清單。 |
|[變更容錯移轉優先順序](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 使用手動容錯移轉觸發程序變更 Azure Cosmos 帳戶的容錯移轉優先順序。 |
|[更新標記](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 更新 Azure Cosmos 帳戶的標記。 |
|[取得帳戶金鑰](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得 Azure Cosmos 帳戶的主要和次要金鑰。 |
|[重新產生帳戶金鑰](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 重新產生 Azure Cosmos 帳戶的主要和次要金鑰。 |
|[列出連接字串](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 取得 Azure Cosmos 帳戶的主要和次要連接字串。 |
|[建立 IP 防火牆](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 Azure Cosmos 帳戶的 IP 防火牆。 |
|[刪除 Azure Cosmos 帳戶](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 刪除 Azure Cosmos 帳戶。 |
|**Azure Cosmos 資料庫**||
| [建立資料庫](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 在 Azure Cosmos 帳戶中建立資料庫。|
| [建立具有共用/資料庫層級輸送量的資料庫](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立與其容器共用資料庫層級輸送量的 Azure Cosmos 資料庫。|
| [列出所有資料庫](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 列出 Azure Cosmos 帳戶中的所有資料庫。|
| [取得資料庫](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 取得 Azure Cosmos 資料庫的屬性。|
|**Azure Cosmos 容器**||
| [建立容器](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立具有專用輸送量的 Azure Cosmos 容器。|
| [建立具有專用輸送量的容器](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立與資料庫中的其他容器共用輸送量的 Azure Cosmos 容器。|
| [建立具有索引原則的容器](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立具有自訂索引原則的 Azure Cosmos 容器。|
| [建立沒有索引原則的容器](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立關閉索引原則的 Azure Cosmos 容器。|
| [建立具有唯一金鑰和 TTL 的容器](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立設定了唯一金鑰條件約束和存留時間的 Azure Cosmos 容器。|
| [建立具有衝突解決方式的容器](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立具有最後寫入為準衝突解決原則的 Azure Cosmos 容器。|
| [列出所有容器](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 列出 Azure Cosmos 資料庫中的所有容器。|
| [取得容器](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 取得 Azure Cosmos 資料庫中某容器的屬性。|
| [刪除容器](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 刪除 Azure Cosmos 資料庫中的所有容器。|
|||