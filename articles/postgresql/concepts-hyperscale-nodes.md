---
title: Azure Database for PostgreSQL-超大規模 (Citus) （預覽） 中的節點
description: 兩種類型的伺服器群組中的節點中。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: c6b948ed63f43f1597103d123be5ed39f42bd276
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077271"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Azure Database for PostgreSQL-超大規模 (Citus) （預覽） 中的節點

裝載類型超大規模 (Citus) （預覽） 適用於 PostgreSQL 伺服器 （稱為節點） 可讓 Azure 資料庫，與彼此協調，以 「 無共用 」 架構。 伺服器群組中的節點共同儲存更多資料，並使用比原本可能在單一伺服器上的 CPU 核心數。 架構也可讓更多節點加入至伺服器群組延展資料庫。

## <a name="coordinator-and-workers"></a>協調器 」 和 「 背景工作角色

每個伺服器群組有協調器節點以及多個背景工作角色。 應用程式會將查詢傳送至協調器節點，將它轉送到相關的背景工作，並彙總其結果。 應用程式不能直接連線到背景工作角色。

針對每個查詢中，協調器會將其路由到單一背景工作節點，或平行它處理跨數個取決於是否存在單一節點或多個所需的資料。 協調器會決定要進行諮詢中繼資料表。 這些資料表會追蹤節點之間的 DNS 名稱和背景工作角色節點的健全狀況和資料的分佈。

## <a name="next-steps"></a>後續步驟
- 了解如何將節點儲存[分散式資料](concepts-hyperscale-distributed-data.md)
