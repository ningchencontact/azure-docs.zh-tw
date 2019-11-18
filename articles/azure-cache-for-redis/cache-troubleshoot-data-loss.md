---
title: 針對 Azure Cache for Redis 中的資料遺失進行疑難排解
description: 瞭解如何使用 Azure Cache for Redis 解決資料遺失問題
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 77493675de0a654d3bb510f7cda22a2abbca0aa2
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121504"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>針對 Azure Cache for Redis 中的資料遺失進行疑難排解

本文討論如何診斷 Azure Cache for Redis 中可能發生的實際或認知資料遺失。

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>

## <a name="partial-loss-of-keys"></a>金鑰的部分遺失

Azure Cache for Redis 在儲存于記憶體後不會隨機刪除金鑰。 不過，它會移除金鑰以回應到期或收回原則，以及明確的索引鍵刪除命令。 已寫入 Premium 或標準 Azure Cache for Redis 實例中主要節點的金鑰，也可能無法立即在複本上使用。 資料會以非同步和非封鎖的方式從主伺服器複寫到複本。

如果您發現金鑰已從快取中消失，請檢查下列可能的原因：

| 原因 | 描述 |
|---|---|
| [金鑰到期](#key-expiration) | 已移除索引鍵，因為它們已設定超時。 |
| [金鑰收回](#key-eviction) | 在記憶體壓力下，會移除索引鍵。 |
| [金鑰刪除](#key-deletion) | 金鑰會由明確的 delete 命令移除。 |
| [非同步複寫](#async-replication) | 因為資料複寫延遲，所以複本上沒有可用的金鑰。 |

### <a name="key-expiration"></a>金鑰到期

Azure Cache for Redis 會在金鑰已指派超時時間且已通過該期間時，自動移除金鑰。 如需 Redis 金鑰到期的詳細資訊，請參閱[過期](http://redis.io/commands/expire)命令檔。 您也可以使用[set](http://redis.io/commands/set)、 [SETEX](https://redis.io/commands/setex)、 [GETSET](https://redis.io/commands/getset)和其他 **\*存放區**命令來設定超時值。

若要取得有多少金鑰已過期的統計資料，請使用[INFO](http://redis.io/commands/info)命令。 [`Stats`] 區段會顯示過期的金鑰總數。 [`Keyspace`] 區段提供具有超時和平均超時值之索引鍵數目的詳細資訊。

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

您也可以查看快取的診斷計量，以查看金鑰遺失的時間和過期金鑰的尖峰之間是否有關聯性。 如需使用 Keyspace 通知或**監視**來檢查這些類型問題的相關資訊，請參閱 [Redis Keyspace](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)遺漏的偵錯工具附錄。

### <a name="key-eviction"></a>金鑰收回

Azure Cache for Redis 需要記憶體空間來儲存資料。 它會清除金鑰，以在必要時釋放可用的記憶體。 當[INFO](http://redis.io/commands/info)命令中的**used_memory**或**used_memory_rss**值接近設定的**Maxmemory**設定時，Azure Cache for Redis 會開始根據快取[原則](http://redis.io/topics/lru-cache)從記憶體收回金鑰。

您可以使用[INFO](http://redis.io/commands/info)命令來監視已收回的金鑰數目：

```
# Stats

evicted_keys:13224
```

您也可以查看快取的診斷計量，以查看金鑰遺失的時間與收回金鑰的尖峰之間是否有關聯性。 如需使用 Keyspace 通知或**監視**來檢查這些類型問題的相關資訊，請參閱 [Redis Keyspace](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix)遺漏的偵錯工具附錄。

### <a name="key-deletion"></a>金鑰刪除

Redis 用戶端可以發出[DEL](http://redis.io/commands/del)或[HDEL](http://redis.io/commands/hdel)命令，以明確地從 Azure Cache for Redis 移除金鑰。 您可以使用[INFO](http://redis.io/commands/info)命令來追蹤刪除作業的數目。 如果已呼叫**DEL**或**HDEL**命令，它們會列在 [`Commandstats`] 區段中。

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>非同步複寫

標準層或進階層中的任何 Azure Cache for Redis 實例都設定了主要節點和至少一個複本。 資料會使用背景處理常式，以非同步方式從主伺服器複製到複本。 [Redis.io](http://redis.io/topics/replication)網站會說明 redis 資料複寫的一般運作方式。 在用戶端經常寫入 Redis 的情況下，可能會發生部分資料遺失，因為這種複寫保證是瞬間的。 例如，如果主要複本在用戶端寫入金鑰*後*關閉 *，但在*背景程式有機會將該金鑰傳送至複本，則當複本接管為新的主伺服器時，金鑰就會遺失。

## <a name="major-or-complete-loss-of-keys"></a>金鑰的主要或完整遺失

如果您的快取中大部分或所有金鑰都消失，請檢查下列可能的原因：

| 原因 | 描述 |
|---|---|
| [金鑰排清](#key-flushing) | 已手動清除金鑰。 |
| [不正確的資料庫選取專案](#incorrect-database-selection) | Azure Cache for Redis 設定為使用非預設的資料庫。 |
| [Redis 實例失敗](#redis-instance-failure) | Redis 伺服器無法使用。 |

### <a name="key-flushing"></a>金鑰排清

用戶端可以呼叫[FLUSHDB](http://redis.io/commands/flushdb)命令來移除*單一*資料庫或[FLUSHALL](http://redis.io/commands/flushall)中的所有金鑰，以移除 Redis 快取中*所有*資料庫的所有索引鍵。 若要找出是否已清除金鑰，請使用[INFO](http://redis.io/commands/info)命令。 [`Commandstats`] 區段會顯示是否已呼叫其中一個 [**清除**] 命令：

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>不正確的資料庫選取專案

Azure Cache for Redis 預設會使用**db0**資料庫。 如果您切換至另一個資料庫（例如， **db1**），並嘗試從中讀取金鑰，Azure Cache for Redis 就不會在該處找到它們。 每個資料庫都是以邏輯方式分隔的單位，而且會存放不同的資料集。 使用[SELECT](http://redis.io/commands/select)命令來使用其他可用的資料庫，並在其中尋找其金鑰。

### <a name="redis-instance-failure"></a>Redis 實例失敗

Redis 是記憶體中的資料存放區。 資料會保留在裝載 Redis 快取的實體或虛擬機器上。 基本層中的 Azure Cache for Redis 實例只會在單一虛擬機器（VM）上執行。 如果該 VM 已關閉，您儲存在快取中的所有資料都會遺失。 

標準和進階層中的快取會在複寫的設定中使用兩個 Vm，以提供更高的復原能力來避免資料遺失。 當這類快取中的主要節點失敗時，複本節點會接管，以自動提供資料。 這些 Vm 位於不同的網域中，以進行錯誤和更新，以將同時無法同時變成無法使用的機會降到最低。 不過，如果發生主要的資料中心中斷，Vm 可能仍在一起。 在這些罕見的情況下，您的資料將會遺失。

請考慮使用[Redis 資料持續](http://redis.io/topics/persistence)性和[異地](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication)複寫，以根據這些基礎結構失敗來改善資料的保護。

## <a name="additional-information"></a>其他資訊

- [針對 Azure Cache for Redis 伺服器端問題進行疑難排解](cache-troubleshoot-server.md)
- [應該使用哪個 Azure Redis 快取供應項目和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
- [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
