---
title: 調整伺服器群組-超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 調整伺服器群組記憶體、磁片和 CPU 資源，以處理增加的負載
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 5d8bbe493887c5340f0943a585eb6ff250bd3728
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977551"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>調整超大規模資料庫（Citus）伺服器群組

適用於 PostgreSQL 的 Azure 資料庫超大規模資料庫（Citus）提供自助式調整來處理增加的負載。 Azure 入口網站可讓您輕鬆地加入新的背景工作節點，以及增加現有節點的容量。

## <a name="add-worker-nodes"></a>新增背景工作節點

若要新增節點，請移至超大規模資料庫（Citus）伺服器群組中的 [**設定**] 索引標籤。  拖曳背景**工作節點計數**的滑杆會變更此值。

![資源滑杆](./media/howto-hyperscale-scaling/01-sliders-workers.png)

按一下 [**儲存**] 按鈕，讓變更的值生效。

> [!NOTE]
> 一旦增加並儲存之後，就無法使用滑杆來減少背景工作節點的數目。

### <a name="rebalance-shards"></a>重新平衡分區

若要利用新加入的節點，您必須重新平衡分散式資料表[分區](concepts-hyperscale-distributed-data.md#shards)，這表示要將某些分區從現有的節點移至新的節點。 請先確認新的背景工作角色已成功完成布建。 然後開機磁碟分割 rebalancer，方法是使用 psql 連接到叢集協調器節點，然後執行：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

函數會重新平衡其引數中名為之資料表的[共置群組](concepts-hyperscale-colocation.md)中的所有資料表。`rebalance_table_shards` 因此，您不需要針對每個分散式資料表呼叫函數，只要在每個共置群組的代表性資料表上呼叫它即可。

## <a name="increase-vcores-or-storage-space"></a>增加虛擬核心或儲存空間

除了新增節點之外，您還可以增加現有節點的功能。 移至超大規模資料庫（Citus）伺服器群組中的 [**設定**] 索引標籤，並拖曳**虛擬核心**和**儲存體**的滑杆來變更所有背景工作角色節點的這些值。 請務必按一下 [**儲存**] 以套用變更。

## <a name="next-steps"></a>後續步驟

深入瞭解伺服器群組[效能選項](concepts-hyperscale-configuration-options.md)。
