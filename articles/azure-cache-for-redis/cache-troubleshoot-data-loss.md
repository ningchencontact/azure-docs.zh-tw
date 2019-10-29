---
title: 針對 Azure Cache for Redis 資料遺失進行疑難排解 |Microsoft Docs
description: 瞭解如何解決 Azure Cache for Redis 的資料遺失問題
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 4fee7c84b394e84369b28d2a4191d0e581f3beba
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044352"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>針對 Azure Cache for Redis 資料遺失進行疑難排解

本節討論如何診斷 Azure Cache for Redis 中可能發生的實際或認知資料遺失。

- [金鑰的部分遺失](#partial-loss-of-keys)
- [金鑰的主要或完整遺失](#major-or-complete-loss-of-keys)

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="partial-loss-of-keys"></a>金鑰的部分遺失

Redis 一旦儲存在記憶體中，就不會隨機刪除索引鍵。 不過，它會移除金鑰，以回應到期或收回原則，以及明確的金鑰刪除命令。 此外，在高階或標準 Azure Cache for Redis 中寫入主要節點的金鑰，可能無法立即在複本上使用。 資料會以非同步和非封鎖的方式從主伺服器複寫到複本。

如果您發現金鑰已從快取中消失，您可以檢查下列內容以查看可能原因：

| 原因 | 描述 |
|---|---|
| [金鑰到期](#key-expiration) | 已移除索引鍵，因為它們已設定超時 |
| [金鑰收回](#key-eviction) | 記憶體壓力下的索引鍵已移除 |
| [金鑰刪除](#key-deletion) | 明確刪除命令會移除金鑰 |
| [非同步複寫](#async-replication) | 因為資料複寫延遲，所以複本上的金鑰無法使用 |

### <a name="key-expiration"></a>金鑰到期

如果金鑰已指派超時時間，且已通過該期間，Redis 會自動將其移除。 您可以在[過期](http://redis.io/commands/expire)命令檔中找到更多有關 Redis 金鑰到期的詳細資料。 Timeout 值也可以透過[set](http://redis.io/commands/set)、 [SETEX](https://redis.io/commands/setex)、 [GETSET](https://redis.io/commands/getset)和其他 \*STORE 命令來設定。

您可以使用[INFO](http://redis.io/commands/info)命令來取得已過期的金鑰統計資料。 [*統計*資料] 區段會顯示過期的金鑰總數。 *Keyspace*區段提供有關具有超時的索引鍵數目和平均超時值的其他資訊。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

此外，您可以查看快取的診斷計量，以查看金鑰遺失的時間和過期金鑰的尖峰之間是否有關聯性。 如需使用 Keyspace 通知或監視來檢查這些類型問題的相關資訊，請參閱[附錄](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)。

### <a name="key-eviction"></a>金鑰收回

Redis 需要記憶體空間來儲存資料。 它會清除金鑰，以在必要時釋放可用的記憶體。 當[INFO](http://redis.io/commands/info)命令中的**used_memory**或**used_memory_rss**值接近設定的**maxmemory**設定時，Redis 會根據快取[原則](http://redis.io/topics/lru-cache)開始收回記憶體中的金鑰。

您可以使用[INFO](http://redis.io/commands/info)命令來監視已收回的金鑰數目。

```
# Stats

evicted_keys:13224
```

此外，您可以查看快取的診斷計量，以查看金鑰遺失的時間與收回金鑰的尖峰之間是否有關聯性。 如需使用 Keyspace 通知或監視來檢查這些類型問題的相關資訊，請參閱[附錄](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)。

### <a name="key-deletion"></a>金鑰刪除

Redis 用戶端可以發出[DEL](http://redis.io/commands/del)或[HDEL](http://redis.io/commands/hdel)命令，以明確地從 Redis 中移除金鑰。 您可以使用[INFO](http://redis.io/commands/info)命令來追蹤刪除作業的數目。 如果已呼叫 DEL 或 HDEL 命令，它們會列在 [ *Commandstats* ] 區段中。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>非同步複寫

標準或進階層中的任何 Azure Cache for Redis 都會設定一個主要節點和至少一個複本。 資料會使用背景處理常式，以非同步方式從主伺服器複製到複本。 [Redis.io](http://redis.io/topics/replication)網站會說明 redis 資料複寫的一般運作方式。 在用戶端經常寫入 Redis 的情況下，部分資料遺失可能是因為這種複寫保證是瞬間的。 比方說，如果主伺服器在用戶端寫入金鑰後 _，但在_背景程式有機會將此金鑰傳送至複本_之後_，則當複本接管做為新的主伺服器時，金鑰就會遺失。

## <a name="major-or-complete-loss-of-keys"></a>金鑰的主要或完整遺失

如果您發現快取中的大部分或所有金鑰都消失，您可以檢查下列內容以查看可能原因：

| 原因 | 描述 |
|---|---|
| [金鑰排清](#key-flushing) | 已手動清除金鑰 |
| [不正確的資料庫選取專案](#incorrect-database-selection) | Redis 設定為使用非預設的資料庫 |
| [Redis 實例失敗](#redis-instance-failure) | Redis 伺服器無法使用 |

### <a name="key-flushing"></a>金鑰排清

用戶端可以呼叫[FLUSHDB](http://redis.io/commands/flushdb)命令來移除**單一**資料庫或[FLUSHALL](http://redis.io/commands/flushall)中的所有金鑰，以移除 Redis 快取中**所有**資料庫的所有索引鍵。 您可以使用[INFO](http://redis.io/commands/info)命令來瞭解是否已清除金鑰。 它會顯示是否已在*Commandstats*區段中呼叫其中一個 FLUSH 命令。

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>不正確的資料庫選取專案

Azure Cache for Redis 預設會使用**db0**資料庫。 如果您切換至另一個資料庫（例如，db1）並嘗試從中讀取金鑰，Redis 將不會在該處找到它們，因為每個資料庫都是以邏輯方式分隔的單位，而且會保留不同的資料集。 使用[SELECT](http://redis.io/commands/select)命令來使用其他可用的資料庫，並在其中尋找其金鑰。

### <a name="redis-instance-failure"></a>Redis 實例失敗

Redis 是記憶體中的資料存放區。 資料會保留在裝載 Redis 的實體或虛擬機器上。 基本層中的 Azure Cache for Redis 實例只會在單一虛擬機器（VM）上執行。 如果該 VM 已關閉，您儲存在快取中的所有資料都會遺失。 標準和進階層中的快取會在複寫的設定中使用兩個 Vm，以提供更高的復原能力來避免資料遺失。 當這類快取中的主要節點失敗時，複本節點將會接管，以自動提供資料。 這些 Vm 位於不同的容錯和更新網域上，可將兩者同時變成無法使用的機率降到最低。 不過，萬一發生主要資料中心中斷的情況，Vm 仍然可以同時在一起。 在這些罕見的情況下，您的資料將會遺失。

您應該考慮使用[Redis 資料持續](http://redis.io/topics/persistence)性和[異地](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)複寫，以根據這些基礎結構失敗來改善資料的保護。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [應該使用哪個 Azure Redis 快取供應項目和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
- [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
