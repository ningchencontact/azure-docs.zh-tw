---
title: 如何針對 Azure Redis 快取問題進行疑難排解 | Microsoft Docs
description: 了解如何解決「Azure Redis 快取」的常見問題。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830002"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>如何針對 Azure Redis 快取問題進行疑難排解

本文可帮助你排查在连接 Azure Redis 缓存实例时可能会遇到的各种问题。

- [客户端故障排除](#client-side-troubleshooting)可帮助你识别和解决在应用程序中连接到缓存时遇到的问题。
- [服务器端故障排除](#server-side-troubleshooting)可帮助你识别和解决 Azure Redis 缓存端发生的问题。
- [数据丢失故障排除](#data-loss-troubleshooting)可帮助你识别和解决在缓存中找不到预期键的事件。
- [StackExchange.Redis 超时异常](#stackexchangeredis-timeout-exceptions)提供有关排查 StackExchange.Redis 库问题的具体指导。

> [!NOTE]
> 本指南中的數個疑難排解步驟包含如何執行 Redis 命令和監視各種效能度量的指示。 如需詳細資訊和指示，請參閱 [其他資訊](#additional-information) 一節中的文章。
>
>

## <a name="client-side-troubleshooting"></a>用戶端疑難排解

本節討論因為用戶端應用程式的某種情況而發生之問題的疑難排解。

- [用戶端上的記憶體壓力](#memory-pressure-on-the-client)
- [流量暴增](#burst-of-traffic)
- [用戶端 CPU 使用量很高](#high-client-cpu-usage)
- [超過用戶端頻寬](#client-side-bandwidth-exceeded)
- [要求/回應大小很大](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>用戶端上的記憶體壓力

客户端计算机上出现的内存压力会导致各种性能问题，这些问题可能会延迟对缓存所发出的响应的处理。 出现内存压力时，系统可能会将数据分页到磁盘。 此「分頁錯誤」  會使系統明顯變慢。

检测客户端上的内存压力：

- 监视计算机上的内存用量，确保所用内存未超过可用内存。
- 监视客户端的 `Page Faults/Sec` 性能计数器。 在正常运行期间，大多数系统会出现某些页面错误。 如果页面错误中存在与请求超时相关的峰值，可能表示出现了内存压力。

可通过多种方式缓解客户端上较高的内存压力：

- 深入分析内存使用模式，并减少客户端上的内存消耗量。
- 将客户端 VM 升级到可提供更多内存的更大大小。

### <a name="burst-of-traffic"></a>流量暴增

流量暴增加上不當的 `ThreadPool` 設定，可能會導致延遲處理 Redis 伺服器已傳送但用戶端上尚未使用的資料。

使用[示例 `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs) 监视 `ThreadPool` 统计信息在不同时间的变化。 可以使用 StackExchange.Redis 发出的 `TimeoutException` 消息（如下所示）做进一步的调查：

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

在上面的异常中，有几个需要注意的问题：

- 請注意，在 `IOCP` 區段和 `WORKER` 區段中，`Busy` 值大於 `Min` 值。 这种差异意味着 `ThreadPool` 设置需要调整。
- 您也可以查看 `in: 64221`。 此值表示客户端的内核套接字层收到了 64,211 字节，但应用程序尚未读取这些字节。 这种差异通常意味着，应用程序（例如 StackExchange.Redis）从网络读取数据的速度没有服务器向你发送数据的速度快。

可以[配置 `ThreadPool` 设置](https://gist.github.com/JonCole/e65411214030f0d823cb)，确保线程池在流量激增的情况下快速扩展。

### <a name="high-client-cpu-usage"></a>用戶端 CPU 使用量很高

客户端 CPU 使用率偏高表示系统跟不上所要求执行的工作的进度。 即使缓存发送响应的速度很快，客户端也可能无法及时处理该响应。

使用 Azure 门户中提供的指标或者通过计算机上的性能计数器监视客户端的系统范围的 CPU 使用率。 请注意不要监视进程 CPU，因为即使单个进程的 CPU 使用率较低，但系统范围的 CPU 使用率也可能很高。 請監看與逾時對應的 CPU 使用量暴增。 CPU 使用率较高可能还会导致 `TimeoutException` 错误消息中出现较大的 `in: XXX` 值，如[流量激增](#burst-of-traffic)部分所述。

> [!NOTE]
> StackExchange.Redis 1.1.603 和更新版本在 `TimeoutException` 錯誤訊息中包含 `local-cpu` 度量。 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
>
>

缓解客户端 CPU 使用率较高的问题：

- 调查出现 CPU 峰值的原因。
- 将客户端升级到可提供更多 CPU 容量的更大 VM 大小。

### <a name="client-side-bandwidth-exceeded"></a>超過用戶端頻寬

用戶端機器可用的網路頻寬多寡可能會有限制 (視其架構而定)。 如果客户端超出可用带宽，则客户端的数据处理速度将赶不上服务器的数据发送速度。 這種情況可能會導致逾時。

使用 [示例 `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)监视带宽使用率在不同时间的变化。 此程式碼可能無法在具有限制權限的某些環境 (例如 Azure 網站) 中順利執行。

若要缓解此问题，请减少网络带宽消耗，或者将客户端 VM 大小提高到可以提供更大网络容量的大小。

### <a name="large-requestresponse-size"></a>要求/回應大小很大

大型的要求/回應可能會導致逾時。 例如，假设你在客户端上配置的超时值为 1 秒。 應用程式同時 (使用相同的實體網路連線) 要求兩個金鑰 (例如 'A' 和 'B')。 大多数客户端支持对请求进行“管道操作”，使得请求“A”和“B”可以逐个发送，而无需等待响应。 伺服器會以相同順序傳回回應。 如果响应“A”较大，可能会消耗掉后续请求的大部分超时时间。

在以下示例中，请求“A”和“B”快速发送到服务器。 服务器开始快速发送响应“A”和“B”。 由于数据传输需要时间，即使服务器的响应速度很快，响应“B”也必须等到响应“A”超时。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

此要求/回應並不容易測量。 可对客户端代码进行检测，以跟踪大型请求和响应。

针对大型响应的解决方法各不相同，但是包括：

1. 优化应用程序以处理大量的小值，而不是处理少量的大值。
    - 比較好的解決方案是將資料分割成相關的較小值。
    - 請參閱文章[redis 的理想值大小範圍為何？是 100KB 太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)如需有關為什麼建議使用較小的值。
1. 增大 VM 的大小以获得更高的带宽能力
    - 提高客户端或服务器 VM 上的带宽可以缩短较大响应的数据传输时间。
    - 将两台计算机上的网络用量与当前 VM 大小的限制进行比较。 只提高服务器上的带宽，或者只提高客户端上的带宽，都不足以解决问题。
1. 增加应用程序使用的连接对象数。
    - 使用轮询方法通过不同的连接对象发出请求。

## <a name="server-side-troubleshooting"></a>服务器端故障排除

本節討論因為快取伺服器的某種情況而發生之問題的疑難排解。

- [伺服器上的記憶體壓力](#memory-pressure-on-the-server)
- [高 CPU 使用率/伺服器負載](#high-cpu-usage--server-load)
- [超過伺服器端頻寬](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>伺服器上的記憶體壓力

伺服器端上的記憶體壓力會導致各種效能問題，而可能延遲處理要求。 出现内存压力时，系统可能会将数据分页到磁盘。 此「分頁錯誤」  會使系統明顯變慢。 此記憶體壓力有幾個可能的原因︰

- 缓存中填充的数据即将达到其最大容量。
- Redis 出现大量内存碎片。 这种碎片往往是存储大型对象造成的，因为 Redis 已针对小型对象进行优化。

Redis 通过 [INFO](https://redis.io/commands/info) 命令公开以下两项统计信息来帮助你识别此问题：“used_memory”和“used_memory_rss”。 可以使用门户[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

可以通过多种可能的更改来帮助确保内存用量正常：

- [設定記憶體原則](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 並在金鑰上設定到期時間。 如果存在内存碎片，则此策略可能还不足够。
- [設定 maxmemory-reserved 值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) ，讓此值大到足以彌補記憶體分散情形。 有关详细信息，请参阅下面附加的[有关内存预留的注意事项](#considerations-for-memory-reservations)。
- 將大型快取物件分割為較小的相關物件。
- 基于指标（例如已用内存）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更多内存容量的更大缓存大小。

#### <a name="considerations-for-memory-reservations"></a>有关内存预留的注意事项

更新内存预留值（例如 maxmemory-reserved）可能会影响缓存性能。 假设某个 53-GB 的缓存中填充了 49-GB 的数据。 将预留值更改为 8 GB 会将系统的最大可用内存降低到 45 GB。 如果 _used_memory_ 或 _used_memory_rss_ 值高于 45 GB，则系统可能会不断逐出数据，直到 _used_memory_ 和 _used_memory_rss_ 均低于 45 GB。 收回會增加伺服器負載並讓記憶體過於分散。

### <a name="high-cpu-usage--server-load"></a>高 CPU 使用率/伺服器負載

服务器负载或 CPU 使用率偏高意味着服务器无法及时处理请求。 服务器可能会减慢响应速度，且无法跟上请求速率。

[监视指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)，例如 CPU 或服务器负载。 請監看與逾時對應的 CPU 使用量暴增。

可以通过做出几项更改来缓解较高的服务器负载：

- 调查哪些因素导致出现 CPU 峰值，例如，正在运行[资源密集型的命令](#expensive-commands)，或者较高的内存压力导致出现页面错误。
- 基于指标（例如 CPU 或服务器负载）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更多 CPU 容量的更大缓存大小。

#### <a name="expensive-commands"></a>资源密集型的命令

并非所有 Redis 命令都是以相同的方式创建的 - 有些命令的开销高于其他命令。 [Redis 命令文档](https://redis.io/commands)介绍了每个命令的时间复杂性。 建议查看针对缓存运行的命令，以了解这些命令对性能造成的影响。 例如，我们经常使用 [KEYS](https://redis.io/commands/keys) 命令，但事先并不知道它是一个 O(N) 操作。 可以使用 [SCAN](https://redis.io/commands/scan) 来避免 KEYS，以降低 CPU 峰值。

使用 [SLOWLOG](https://redis.io/commands/slowlog) 命令可以测量正在对服务器执行的命令的开销。

### <a name="server-side-bandwidth-exceeded"></a>超出服务器端带宽

不同的缓存大小具有不同的网络带宽容量。 如果服务器超出可用带宽，则数据无法快速发送到客户端。 客户端请求可能会超时，因为服务器无法以足够快的速度将数据推送到客户端。

可以使用“缓存读取”和“缓存写入”指标来查看使用的服务器端带宽量。 可以在门户中[查看这些指标](cache-how-to-monitor.md#view-metrics-with-azure-monitor)。

缓解网络带宽用量即将达到最大容量的情况：

- 更改客户端调用行为，以降低网络需求。
- 基于指标（例如缓存读取或缓存写入）[创建警报](cache-how-to-monitor.md#alerts)，以提前收到有关潜在影响的通知。
- [扩展](cache-how-to-scale.md)到可提供更高网络带宽容量的更大缓存大小。

## <a name="data-loss-troubleshooting"></a>数据丢失故障排除

我預期「Azure Redis 快取」執行個體中會有某些資料，但是裡面似乎沒有。

如需可能的原因和解決方案，請參閱 [我在 Redis 中的資料怎麼了？](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 逾時例外狀況

StackExchange.Redis 使用名为 `synctimeout` 的配置设置进行同步操作，该设置的默认值为 1000 毫秒。 如果同步调用未在此时间内完成，StackExchange.Redis 客户端会引发类似于以下示例的超时错误：

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

此錯誤訊息包含的度量可協助指出問題的原因和可能的解決方案。 下表包含有關錯誤訊息度量的詳細資料。

| 錯誤訊息度量 | 詳細資料 |
| --- | --- |
| inst |在上一個時間配量中：已發出 0 個命令 |
| mgr |套接字管理器正在执行 `socket.select`，即，它正在请求 OS 指示一个需要执行某种操作的套接字。 读取器并未主动从网络读取数据，因为它认为不需执行任何操作 |
| 佇列 |總共有 73 個進行中的作業 |
| qu |正在进行的操作中，有 6 个操作位于未发送队列中，而尚未写入到出站网络 |
| qs |正在进行的操作中，有 67 个操作已发送给服务器，但尚未得到响应。 回應可能是 `Not yet sent by the server` 或 `sent by the server but not yet processed by the client.` |
| qc |正在进行的操作中，有 0 个操作已看到回复，但尚未标记为完成，因为它们正在完成循环中等待 |
| wr |存在活动的写入器（这意味着系统不会忽略这 6 个尚未发送的请求）字节/活动写入器 |
| 了嗎 |沒有作用中的讀取器，在 NIC 位元組/activereader 上可供讀取的位元組為零 |

### <a name="steps-to-investigate"></a>調查步驟

1. 作为最佳做法，在使用 StackExchange.Redis 客户端时，请确保遵循以下模式进行连接。

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    如需詳細資訊，請參閱 [使用 StackExchange.Redis 來連線到快取](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)。

1. 确保服务器和客户端应用程序位于 Azure 中的同一区域。 例如，您可能會收到逾時快取位在美國東部，但在用戶端位於美國西部和要求未完成內`synctimeout`間隔，或您可能會收到逾時在偵錯從本機開發電腦。 

    強烈建議您讓快取和用戶端位在相同的 Azure 區域中。 如果您有包含跨區域呼叫的案例，您應該將 `synctimeout` 間隔設定為大於預設值 1000 毫秒間隔的值，方法是在連接字串中加入 `synctimeout` 屬性。 以下示例演示了 Azure Redis 缓存提供的 StackExchange.Redis 连接字符串代码片段，其中的 `synctimeout` 为 2000 毫秒。

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
1. 如果请求受服务器或客户端上的带宽限制的约束，则需要更长的时间才能完成，因此可能会导致超时。 若要了解超时是否是服务器上的网络带宽造成的，请参阅[超出服务器端带宽](#server-side-bandwidth-exceeded)。 若要了解超时是否是客户端网络带宽造成的，请参阅[超出客户端带宽](#client-side-bandwidth-exceeded)。
1. 您是否在伺服器或用戶端上受到 CPU 限制？

   - 检查是否受到了客户端上的 CPU 消耗量的约束。 较高的 CPU 可能会导致请求无法在 `synctimeout` 时间间隔内得到处理，从而导致请求超时。改用較大的用戶端或分散負載有助於控制此問題。
   - 监视 `CPU` [缓存性能指标](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)，检查是否受到了服务器上的 CPU 消耗量的约束。 如果请求传入时 Redis 受到 CPU 消耗量的约束，则可能会导致这些请求超时。若要解決此狀況，您可以將負載分散在高階快取的多個分區中，或升級至較大的大小或定價層。 如需詳細資訊，請參閱 [超過伺服器端頻寬](#server-side-bandwidth-exceeded)。
1. 伺服器上是否有命令需要很長的處理時間？ 在 Redis 服务器上花费很长时间处理请求的命令可能会导致超时。 有关长时间运行的命令的详细信息，请参阅[资源密集型的命令](#expensive-commands)。 可以使用 redis-cli 客户端或 [Redis 控制台](cache-configure.md#redis-console)连接到 Azure Redis 缓存实例。 然后，运行 [SLOWLOG](https://redis.io/commands/slowlog) 命令查看是否存在比预期速度更慢的请求。 Redis 伺服器和 StackExchange.Redis 最適合許多小型要求，而非少數幾個大型要求。 將資料分割成較小的區塊可以改善這些問題。

    有关使用 redis-cli 和 stunnel 连接到缓存 SSL 终结点的信息，请参阅博客文章：[Announcing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)（宣布推出适用于 Redis 的 ASP.NET 会话状态提供程序预览版）。
1. 高 Redis 伺服器負載可能會導致逾時。 您可以藉由監視 `Redis Server Load` [快取效能度量](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)來監視伺服器負載。 伺服器負載為 100 (最大值) 表示 Redis 伺服器正忙碌處理要求，並沒有閒置的時間。 若要確認特定要求是否會佔用所有伺服器功能，請執行 SlowLog 命令，如上一段所述。 如需詳細資訊，請參閱「高 CPU 使用率/伺服器負載」。
1. 用戶端上是否有其他任何事件可能導致網路問題？ 常见事件包括：增加或减少客户端实例的数目、部署新的客户端版本，或启用自动缩放。 我们在测试中发现，自动缩放或扩展/缩减可能会导致出站网络连接断开几秒。 StackExchange.Redis 程式碼對於這類事件具有復原能力，因此將會重新連線。 重新连接时，队列中的所有请求可能超时。
1. 在向缓存发出多个小型请求之前，是否存在导致超时的大型请求？ 错误消息中的参数 `qs` 会告知，有多少个请求已从客户端发送到服务器，但尚未处理响应。 這個值會不斷成長，因為 StackExchange.Redis 使用單一 TCP 連線，而且一次只能讀取一個回應。 即使第一个操作超时，也不会阻止与服务器之间来回发送数据。 在完成大型请求之前，系统会阻止其他请求，从而可能导致超时。 有一個解決方案是確保快取足以容納工作負載，並將較大的值分割成較小的區塊，以將逾時的機會降到最低。 另一個可能的解決方案是在用戶端中使用 `ConnectionMultiplexer` 物件集區，並在傳送新要求時選擇最少負載的 `ConnectionMultiplexer`。 通过多个连接对象进行加载应该可以防止单次超时导致其他请求也发生超时。
1. 如果使用 `RedisSessionStateProvider`，请确保正确设置重试超时。 `retryTimeoutInMilliseconds` 應高於 `operationTimeoutInMilliseconds`，否則不會發生任何重試。 在下列範例中， `retryTimeoutInMilliseconds` 已設定為 3000。 如需詳細資訊，請參閱[適用於 Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)和[如何使用工作階段狀態提供者和輸出快取提供者的設定參數](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) \(英文\)。

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. 透過[監視](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` 和 `Used Memory`，檢查「Azure Redis 快取」伺服器上的記憶體使用量。 如果已備有收回原則，Redis 就會在 `Used_Memory` 達到快取大小時開始收回金鑰。 理想情況下，`Used Memory RSS` 應該只稍微高於 `Used memory`。 差异过大意味着出现内存碎片（内部或外部）。 當 `Used Memory RSS` 小於 `Used Memory` 時，則表示作業系統已交換部分快取記憶體。 如果發生此交換情況，您應該就會遇到顯著的延遲。 由于 Redis 无法控制如何将其分配映射到内存页，`Used Memory RSS` 偏高通常是内存用量出现高峰而造成的。 当 Redis 服务器释放内存时，分配器会回收该内存，但不一定会将内存重新分配到系统。 `Used Memory` 值和作業系統報告的記憶體耗用量可能會有差異。 内存可能已由 Redis 使用并释放，但尚未重新分配到系统。 为了帮助缓解内存问题，可执行以下步骤：

   - 将缓存升级到更大的大小，以免系统中的内存限制导致运行受阻。
   - 設定金鑰的到期時間，以便主動收回較舊的值。
   - 監視 `used_memory_rss` 快取計量。 当此值接近缓存大小时，可能会开始出现性能问题。 将数据分配到多个分片（如果使用高级缓存），或者升级到更大的缓存大小。

   如需詳細資訊，請參閱 [伺服器上的記憶體壓力](#memory-pressure-on-the-server)。

## <a name="additional-information"></a>其他資訊

- [應該使用哪個 Azure Redis 快取供應項目和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何效能評定和測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
