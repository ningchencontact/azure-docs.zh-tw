---
title: 高可用性–超大規模資料庫（Citus）-適用於 PostgreSQL 的 Azure 資料庫
description: 高可用性和嚴重損壞修復概念
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975528"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>適用於 PostgreSQL 的 Azure 資料庫中的高可用性–超大規模資料庫（Citus）

高可用性 (HA) 能藉由維護伺服器群組中每個節點的待命複本，來避免資料庫停機。 如果節點關閉，超大規模就會將失敗節點的輸入連線切換至其待命節點。 容錯移轉會在數分鐘內發生，升階的節點一律會透過 PostgreSQL 同步串流複寫獲得更新的資料。

若要利用協調器節點上的 HA，資料庫應用程式必須偵測並重試中斷的連接和失敗的交易。 新升級的協調器將可透過相同的連接字串來存取。

復原可以分成三個階段：偵測、容錯移轉和完整復原。  超大規模資料庫會在每個節點上執行定期健全狀況檢查，並在四次失敗檢查之後判斷節點已關閉。 超大規模資料庫接著會將待命升級為主要節點狀態（容錯移轉），並布建新的待命。
串流複寫會開始，使新節點保持在最新狀態。  當所有資料都已複寫之後，節點就已達到完整復原。

### <a name="next-steps"></a>後續步驟

- 瞭解如何在超大規模資料庫伺服器群組中[啟用高可用性](howto-hyperscale-high-availability.md)。
