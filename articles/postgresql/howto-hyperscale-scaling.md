---
title: 調整適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）伺服器群組
description: 調整伺服器群組記憶體、磁片和 CPU 資源，以處理增加的負載
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262488"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>調整超大規模資料庫（Citus）伺服器群組

適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）提供自助式調整來處理增加的負載。 Azure 入口網站可讓您輕鬆地加入新的背景工作節點。

若要這麼做，請移至超大規模資料庫（Citus）伺服器群組中的 [**設定**] 索引標籤。
拖曳 [背景**工作角色節點計數**] 的滑杆來變更值。

![資源滑杆](./media/howto-hyperscale-scaling/01-sliders-workers.png)

按一下 [儲存] 按鈕，讓變更的值生效。

> [!NOTE]
> 一旦增加並儲存之後，就無法使用滑杆來減少背景工作節點的數目。
>
> 此外，在具有此使用者介面的協調器或背景工作上，尚未調整虛擬核心和 Storage。 如果需要在協調器或背景工作節點上調整計算，請開啟支援票證。

若要利用新加入的節點，您必須重新平衡分散式資料表[分區](concepts-hyperscale-distributed-data.md#shards)，這表示要將某些分區從現有的節點移至新的節點。 若要開機磁碟分割 rebalancer，請使用 psql 連接到叢集協調器節點，然後執行：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

函數會重新平衡其引數中名為之資料表的[共置群組](concepts-hyperscale-colocation.md)中的所有資料表。`rebalance_table_shards` 因此，您不需要針對每個分散式資料表呼叫函數，只要在每個共置群組的代表性資料表上呼叫它即可。

## <a name="next-steps"></a>後續步驟

深入瞭解伺服器群組[效能選項](concepts-hyperscale-configuration-options.md)。
