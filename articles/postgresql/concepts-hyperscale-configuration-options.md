---
title: 適用於 PostgreSQL-超大規模 (Citus) （預覽） 的 azure 資料庫效能選項
description: 超大規模 (Citus) 的伺服器群組，包括節點之計算、 儲存體，以及區域選項。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077286"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>適用於 PostgreSQL-超大規模 (Citus) （預覽） 的 azure 資料庫效能選項

## <a name="compute-and-storage"></a>計算和儲存體
 
超大規模 (Citus) 的伺服器群組中，您可以選取計算與儲存體設定的背景工作角色節點的獨立和協調器節點。  計算資源會以 vCore 的形式提供，vCore 代表了基礎硬體的邏輯 CPU。 佈建的儲存體大小是指您的超大規模 (Citus) 的伺服器群組中的協調器 」 和 「 背景工作節點都可使用的容量。 儲存體包括資料庫檔案、 暫存檔案、 交易記錄檔和 Postgres 伺服器記錄檔。 總數也佈建的儲存體定義的每個背景工作角色和協調器節點可用的 I/O 容量。
 
|                       | 背景工作角色節點           | 協調器節點      |
|-----------------------|-----------------------|-----------------------|
| 計算，虛擬核心       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| 每個虛擬核心，GiB 的記憶體 | 8                     | 4                     |
| 儲存體大小 TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| 儲存體類型          | 一般用途 (SSD) | 一般用途 (SSD) |
| IOPS                  | 最多 3 個 IOPS/GiB      | 最多 3 個 IOPS/GiB      |


## <a name="regions"></a>Regions
超大規模 (Citus) 的伺服器群組會在下列 Azure 區域上市：
* 美國東部 2
* 東南亞
* 西歐
* 美國西部 2

## <a name="pricing"></a>價格
如需最新的定價資訊，請參閱服務的[定價頁面](https://azure.microsoft.com/pricing/details/postgresql/)。
若要查看您想要組態的成本[Azure 入口網站](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)上會顯示每月成本**設定** 索引標籤會根據您選取的選項。 如果您沒有 Azure 訂用帳戶，則可以使用 Azure 價格計算機來取得估計的價格。 上[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)網站上，選取**加入項目**，展開**資料庫**類別目錄，然後選擇 **適用於 PostgreSQL 的 Azure 資料庫超大規模 (Citus)** 以自訂選項。
 
## <a name="next-steps"></a>後續步驟
了解如何[入口網站中建立超大規模 (Citus) 伺服器群組](quickstart-create-hyperscale-portal.md)。
