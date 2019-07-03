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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60830002"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>如何針對 Azure Redis 快取問題進行疑難排解

這篇文章可協助您針對不同類別的 Azure 快取使用 Redis 執行個體進行連接時，您可能會遇到的問題進行疑難排解。

- [用戶端疑難排解](#client-side-troubleshooting)能夠協助識別並解決應用程式連接到您的快取中的問題。
- [伺服器端疑難排解](#server-side-troubleshooting)能夠協助識別並解決 Azure 快取，Redis 端發生的問題。
- [資料遺失疑難排解](#data-loss-troubleshooting)能夠協助識別並解決的事件索引鍵必須是但在您的快取中找不到的位置。
- [StackExchange.Redis 逾時例外狀況](#stackexchangeredis-timeout-exceptions)提供使用 StackExchange.Redis 程式庫進行疑難排解的特定指引。

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

用戶端電腦上的記憶體壓力會導致各種效能問題可能會延遲處理從快取的回應。 到達記憶體壓力時系統可能會分頁到磁碟的資料。 此「分頁錯誤」  會使系統明顯變慢。

若要偵測用戶端上的記憶體壓力：

- 監視電腦上的記憶體使用量，以確定它不會超過可用記憶體。
- 監視用戶端的`Page Faults/Sec`效能計數器。 一般作業期間，大部分的系統有一些分頁錯誤。 尖峰與要求逾時對應的分頁錯誤可能表示記憶體不足的壓力。

數種方式，可以減輕在用戶端上的高記憶體壓力：

- 深入探索您的記憶體使用量模式，以減少用戶端上的記憶體耗用量。
- 將用戶端 VM 升級為較大的大小，具有更多記憶體。

### <a name="burst-of-traffic"></a>流量暴增

流量暴增加上不當的 `ThreadPool` 設定，可能會導致延遲處理 Redis 伺服器已傳送但用戶端上尚未使用的資料。

使用[如這裡所提供的`ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs) 程式碼，監視 `ThreadPool` 統計資料如何隨著時間變更。 您可以使用`TimeoutException`若要進一步調查來自 StackExchange.Redis 的訊息如下所示：

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

在上述訊息中，有幾個有趣的問題︰

- 請注意，在 `IOCP` 區段和 `WORKER` 區段中，`Busy` 值大於 `Min` 值。 有此差異表示 `ThreadPool` 設定需要調整。
- 您也可以查看 `in: 64221`。 這個值表示已在用戶端的核心通訊端層接收到 64,211 位元組，但還沒有已讀取的應用程式。 有此差異通常表示應用程式從網路讀取資料的速度，比伺服器傳送資料給您的速度還慢。

設定 [ThreadPool 設定`ThreadPool`以確保執行緒集區會在流量暴增的情況下相應增加。

### <a name="high-client-cpu-usage"></a>用戶端 CPU 使用量很高

用戶端很高 CPU 使用量，指出系統無法跟上會要求您執行的工作。 即使快取快速地傳送回應，用戶端可能無法處理及時回應。

監視用戶端使用 Azure 入口網站中或透過在電腦上的效能計數器的可用計量的全系統 CPU 使用量。 請小心不要監視*程序*CPU 因為單一處理序可以有低 CPU 使用率，但整個系統 CPU 卻很高。 請監看與逾時對應的 CPU 使用量暴增。 高 CPU 也可能會導致高`in: XXX`中的值`TimeoutException`錯誤訊息中所述[流量暴增](#burst-of-traffic)一節。

> [!NOTE]
> StackExchange.Redis 1.1.603 和更新版本在 `TimeoutException` 錯誤訊息中包含 `local-cpu` 度量。 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
>
>

若要降低用戶端的高 CPU 使用率：

- 調查導致 CPU 尖峰的原因。
- 您的用戶端升級至較大的 VM 大小，具有更多的 CPU 容量。

### <a name="client-side-bandwidth-exceeded"></a>超過用戶端頻寬

用戶端機器可用的網路頻寬多寡可能會有限制 (視其架構而定)。 如果用戶端多載網路容量而超過可用的頻寬，則資料在用戶端上的處理速度不會和伺服器傳送資料的速度一樣快。 這種情況可能會導致逾時。

使用 [如這裡所提供的`BandwidthLogger`程式碼，監視頻寬使用量如何隨著時間變更。 此程式碼可能無法在具有限制權限的某些環境 (例如 Azure 網站) 中順利執行。

若要減輕，減少網路頻寬耗用量或增加用戶端具有更多的網路容量的 VM 大小。

### <a name="large-requestresponse-size"></a>要求/回應大小很大

大型的要求/回應可能會導致逾時。 例如，假設您在用戶端上設定的逾時值為 1 秒。 應用程式同時 (使用相同的實體網路連線) 要求兩個金鑰 (例如 'A' 和 'B')。 大部分的用戶端支援以要求 「 管線 」，'A' 和 'B' 這兩個要求傳送至何處逐一接連而不需等待其回應。 伺服器會以相同順序傳回回應。 如果回應 'A' 很大，它可以用掉大部分的更新要求的逾時。

在下列範例中，'A' 和 'B' 的要求會快速伺服器傳送。 傳送回應 'A' 和 'B' 快速啟動伺服器。 資料傳輸的時間，因為 'B' 必須等待回應 'A' 後面的回應逾時即使伺服器已迅速回應。

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

此要求/回應並不容易測量。 基本上，您必須檢測用戶端程式碼來追蹤大量要求和回應。

大型回應大小的解決方法都各不相同，但包含：

1. Redis 最適合大量的較小值，而不是少數幾個較大值。
    - 比較好的解決方案是將資料分割成相關的較小值。
    - 請參閱 [Redis 的理想值大小範圍為何？是 100KB 太大？](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)如需有關為什麼建議使用較小的值。
1. 增加您的 VM，以取得更高的頻寬功能的大小
    - 您的用戶端或伺服器 VM 上的更多頻寬可能會減少較大回應的資料傳輸時間。
    - 比較兩台電腦上目前的 VM 大小的限制，您目前的網路使用量。 只有在伺服器上，或只在用戶端的更多頻寬可能不夠。
1. 增加應用程式所使用的連接物件的數目。
    - 您可以使用循環配置資源方法，透過不同的連接物件提出要求。

## <a name="server-side-troubleshooting"></a>伺服器端疑難排解

本節討論因為快取伺服器的某種情況而發生之問題的疑難排解。

- [伺服器上的記憶體壓力](#memory-pressure-on-the-server)
- [高 CPU 使用率/伺服器負載](#high-cpu-usage--server-load)
- [超過伺服器端頻寬](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>伺服器上的記憶體壓力

伺服器端上的記憶體壓力會導致各種效能問題，而可能延遲處理要求。 到達記憶體壓力時系統可能會分頁到磁碟的資料。 此「分頁錯誤」  會使系統明顯變慢。 此記憶體壓力有幾個可能的原因︰

- 快取填滿接近其最大容量的資料。
- Redis 遇到高度記憶體分散情形。 此片段通常因儲存大型物件，因為 Redis 最適合小型物件。

Redis 會公開兩個統計資料，透過[資訊](https://redis.io/commands/info)命令，可協助您識別此問題: 「 used_memory"和"used_memory_rss 」。 您可以[檢視這些計量](cache-how-to-monitor.md#view-metrics-with-azure-monitor)使用入口網站。

您可以進行幾項可能的變更，以協助讓記憶體使用量保持良好狀況︰

- [設定記憶體原則](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) 並在金鑰上設定到期時間。 請注意，如果記憶體分散，這麼做可能還不夠。
- [設定 maxmemory-reserved 值](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) ，讓此值大到足以彌補記憶體分散情形。 如需詳細資訊，請參閱額外[考量記憶體保留](#considerations-for-memory-reservations)如下。
- 將大型快取物件分割為較小的相關物件。
- [建立警示](cache-how-to-monitor.md#alerts)計量，例如在使用的記憶體要及早收到可能的影響。
- [擴展](cache-how-to-scale.md)具有更多的記憶體容量更大的快取大小。

#### <a name="considerations-for-memory-reservations"></a>記憶體保留的考量

更新記憶體保留值，例如 maxmemory 保留，可能會影響快取效能。 假設您有 53 GB 快取填滿 49 GB 的資料。 將保留值變更為 8 GB，會卸除系統的最大可用的記憶體至 45 GB。 如果_used_memory_或是_used_memory_rss_值高於 45 GB，系統可能會等到收回資料_used_memory_並_used_memory_rss_都低於 45 GB。 收回會增加伺服器負載並讓記憶體過於分散。

### <a name="high-cpu-usage--server-load"></a>高 CPU 使用率/伺服器負載

高的伺服器負載或 CPU 使用量則表示伺服器無法處理要求 neodpověděl včas。 伺服器可能回應太慢而無法跟上要求率。

[監視計量](cache-how-to-monitor.md#view-metrics-with-azure-monitor)例如 CPU 或伺服器的負載。 請監看與逾時對應的 CPU 使用量暴增。

有幾項變更，您可以對減輕高的伺服器負載：

- 調查導致 CPU 尖峰，例如執行的原因[昂貴命令](#expensive-commands)或因為高記憶體不足的壓力而判定為失敗的頁面。
- [建立警示](cache-how-to-monitor.md#alerts)上計量，例如 CPU 或伺服器負載會提早通知可能的影響。
- [擴展](cache-how-to-scale.md)具有更多的 CPU 容量更大的快取大小。

#### <a name="expensive-commands"></a>昂貴的命令

並非所有的 Redis 命令會建立方式都相同-有些是比其他執行耗費更多資源。 [文件的 Redis 命令](https://redis.io/commands)顯示每個命令的時間複雜性。 建議您檢閱您的快取，以了解這些命令的效能影響上正在執行的命令。 比方說，[金鑰](https://redis.io/commands/keys)命令通常用不需要知道這 o （n） 作業。 您可以使用，以避免索引鍵[掃描](https://redis.io/commands/scan)來減少 CPU 尖峰。

使用[SLOWLOG](https://redis.io/commands/slowlog)命令時，您可以測量昂貴的伺服器正在執行的命令。

### <a name="server-side-bandwidth-exceeded"></a>超過伺服器端頻寬

不同的快取大小會有不同的網路頻寬容量。 如果伺服器超過可用的頻寬，則資料不會一樣快地傳送至用戶端。 用戶端要求無法逾時時間，因為伺服器無法將資料推送至用戶端速度不夠快。

「 快取讀取 」 與 「 快取寫入 」 計量可以用來查看正在使用伺服器端頻寬多寡。 您可以[檢視這些計量](cache-how-to-monitor.md#view-metrics-with-azure-monitor)入口網站中。

若要降低的情況下的網路頻寬使用量接近容量上限：

- 若要降低網路需求的用戶端呼叫行為變更。
- [建立警示](cache-how-to-monitor.md#alerts)度量，例如快取讀取或潛在的影響提早通知的快取寫入。
- [擴展](cache-how-to-scale.md)具有更多的網路頻寬容量更大的快取大小。

## <a name="data-loss-troubleshooting"></a>資料遺失進行疑難排解

我預期「Azure Redis 快取」執行個體中會有某些資料，但是裡面似乎沒有。

如需可能的原因和解決方案，請參閱 [我在 Redis 中的資料怎麼了？](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) 。

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis 逾時例外狀況

StackExchange.Redis 使用名為 `synctimeout` 的組態設定來進行預設值為 1000 毫秒的同步作業。 如果同步呼叫未在約定的時間內完成，StackExchange.Redis 用戶端會擲回類似下列範例的逾時錯誤：

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

此錯誤訊息包含的度量可協助指出問題的原因和可能的解決方案。 下表包含有關錯誤訊息度量的詳細資料。

| 錯誤訊息度量 | 詳細資料 |
| --- | --- |
| inst |在上一個時間配量中：已發出 0 個命令 |
| mgr |通訊端管理員正在進行`socket.select`，這表示它會要求 OS 指出有一點的通訊端。 讀取器未主動從網路讀取，因為它不認為有工作需要執行 |
| queue |總共有 73 個進行中的作業 |
| qu |在進行中的作業中，有 6 個位於未傳送的佇列，尚未寫入至輸出網路 |
| qs |在進行中的作業中，有 67 個已傳送至伺服器，但尚未取得回應。 回應可能是 `Not yet sent by the server` 或 `sent by the server but not yet processed by the client.` |
| qc |在進行中的作業中，有 0 個已看見回覆，但尚未標示為已完成，因為正在等候完成迴圈 |
| wr |有作用的寫入器 (亦即不會忽略 6 個未傳送的要求) 位元組/activewriter |
| 了嗎 |沒有作用中的讀取器，在 NIC 位元組/activereader 上可供讀取的位元組為零 |

### <a name="steps-to-investigate"></a>調查步驟

1. 最佳作法是確定您在使用 StackExchange.Redis 用戶端來連線時，使用的是下列模式。

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

1. 確定 Azure Redis 快取和用戶端應用程式位在 Azure 中的相同區域。 例如，當快取位在美國東部，但用戶端位在美國西部，而要求未在 `synctimeout` 間隔內完成時，您可能會收到逾時，或者當您從本機開發電腦進行偵錯時，您可能會收到逾時。 

    強烈建議您讓快取和用戶端位在相同的 Azure 區域中。 如果您有包含跨區域呼叫的案例，您應該將 `synctimeout` 間隔設定為大於預設值 1000 毫秒間隔的值，方法是在連接字串中加入 `synctimeout` 屬性。 下列範例顯示連接字串的程式碼片段為使用 redis Azure 快取所提供的 StackExchange.Redis`synctimeout`為 2000 毫秒。

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. 請確定您使用最新版的 [StackExchange.Redis NuGet 封裝](https://www.nuget.org/packages/StackExchange.Redis/)。 程式碼中的錯誤會經常修正，以便更能應付逾時問題，因此請務必要擁有最新版本。
1. 如果您的要求都受限於伺服器或用戶端上的頻寬限制，花較長，才能完成，而且可能會導致逾時。 若要確認逾時是不是因為伺服器上的網路頻寬所導致，請參閱 [超過伺服器端頻寬](#server-side-bandwidth-exceeded)。 若要確認逾時是不是因為用戶端網路頻寬所導致，請參閱[超過用戶端頻寬](#client-side-bandwidth-exceeded)。
1. 您是否在伺服器或用戶端上受到 CPU 限制？

   - 如果您受到 CPU 限制在您的用戶端上的檢查。 高 CPU 可能會導致無法處理內要求`synctimeout`間隔和逾時要求的原因。改用較大的用戶端或分散負載有助於控制此問題。
   - 確認您是否在伺服器上受到 CPU 限制，方法是監視 `CPU` [快取效能度量](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)。 如果要求在 Redis 受到 CPU 限制時傳入，則可能會造成這些要求逾時。若要解決此狀況，您可以將負載分散在高階快取的多個分區中，或升級至較大的大小或定價層。 如需詳細資訊，請參閱 [超過伺服器端頻寬](#server-side-bandwidth-exceeded)。
1. 伺服器上是否有命令需要很長的處理時間？ Redis 伺服器上處理時間很長的長時間執行命令可能會導致逾時。 如需長時間執行命令的詳細資訊，請參閱[昂貴命令](#expensive-commands)。 您可以連接到您的 Azure 快取，Redis 執行個體使用 redis-cli 用戶端或[Redis 主控台](cache-configure.md#redis-console)。 然後，執行[SLOWLOG](https://redis.io/commands/slowlog)命令，以查看是否有要求得比預期慢。 Redis 伺服器和 StackExchange.Redis 最適合許多小型要求，而非少數幾個大型要求。 將資料分割成較小的區塊可以改善這些問題。

    如需使用 redis-cli 和 stunnel 連線到 Azure Redis 快取 SSL 端點的相關資訊，請參閱 [宣佈 Redis 預覽版本的 ASP.NET 工作階段狀態提供者](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) 部落格文章。
1. 高 Redis 伺服器負載可能會導致逾時。 您可以藉由監視 `Redis Server Load` [快取效能度量](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)來監視伺服器負載。 伺服器負載為 100 (最大值) 表示 Redis 伺服器正忙碌處理要求，並沒有閒置的時間。 若要確認特定要求是否會佔用所有伺服器功能，請執行 SlowLog 命令，如上一段所述。 如需詳細資訊，請參閱「高 CPU 使用率/伺服器負載」。
1. 用戶端上是否有其他任何事件可能導致網路問題？ 常見的事件包括： 用戶端執行個體數目相應增加或相應減少部署新版用戶端或啟用自動調整。 在我們的測試，我們發現，自動調整或相應增加/減少可能會導致輸出網路連線會中斷幾秒鐘的時間。 StackExchange.Redis 程式碼對於這類事件具有復原能力，因此將會重新連線。 同時重新連接後，在佇列中的任何要求可以逾時。
1. 針對 Redis 快取，在幾個小型要求之前是否有大型要求已逾時？ 錯誤訊息中的參數 `qs` 會告訴您有多少要求已從用戶端傳送到伺服器，但尚未處理回應。 這個值會不斷成長，因為 StackExchange.Redis 使用單一 TCP 連線，而且一次只能讀取一個回應。 即使第一項作業已逾時，它不會停止傳送到或從伺服器的詳細資料。 其他要求會遭到封鎖，直到在大型要求已完成，並可能會導致逾時。 有一個解決方案是確保快取足以容納工作負載，並將較大的值分割成較小的區塊，以將逾時的機會降到最低。 另一個可能的解決方案是在用戶端中使用 `ConnectionMultiplexer` 物件集區，並在傳送新要求時選擇最少負載的 `ConnectionMultiplexer`。 跨多個連接物件載入應該防止單一逾時造成其他要求也逾時。
1. 如果您使用 `RedisSessionStateProvider`，請確定您已正確設定重試逾時。 `retryTimeoutInMilliseconds` 應高於 `operationTimeoutInMilliseconds`，否則不會發生任何重試。 在下列範例中， `retryTimeoutInMilliseconds` 已設定為 3000。 如需詳細資訊，請參閱[適用於 Azure Redis 快取的 ASP.NET 工作階段狀態提供者](cache-aspnet-session-state-provider.md)和[如何使用工作階段狀態提供者和輸出快取提供者的設定參數](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration) \(英文\)。

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

1. 透過[監視](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` 和 `Used Memory`，檢查「Azure Redis 快取」伺服器上的記憶體使用量。 如果已備有收回原則，Redis 就會在 `Used_Memory` 達到快取大小時開始收回金鑰。 理想情況下，`Used Memory RSS` 應該只稍微高於 `Used memory`。 差異很大表示記憶體分散 (內部或外部)。 當 `Used Memory RSS` 小於 `Used Memory` 時，則表示作業系統已交換部分快取記憶體。 如果發生此交換情況，您應該就會遇到顯著的延遲。 因為 Redis 無法控制其配置對應到記憶體分頁的方式，高 `Used Memory RSS` 通常是記憶體使用量突然增加的結果。 當 Redis 伺服器，以釋放記憶體時，配置器會耗用記憶體，但是它可能會或可能不會重新提供記憶體給系統。 `Used Memory` 值和作業系統報告的記憶體耗用量可能會有差異。 記憶體可能已使用和釋出 redis，但未交回給系統。 若要降低記憶體問題，您可以執行下列步驟：

   - 將快取升級為更大的大小，以免達到系統的記憶體限制。
   - 設定金鑰的到期時間，以便主動收回較舊的值。
   - 監視 `used_memory_rss` 快取計量。 當這個值接近其快取的大小時，您可能會開始看到效能問題。 請將資料分散到多個分區 (如果您使用高階快取)，或升級至較大的快取大小。

   如需詳細資訊，請參閱 [伺服器上的記憶體壓力](#memory-pressure-on-the-server)。

## <a name="additional-information"></a>其他資訊

- [應該使用哪個 Azure Redis 快取供應項目和大小？](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [如何效能評定和測試我快取的效能？](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [如何執行 Redis 命令？](cache-faq.md#how-can-i-run-redis-commands)
- [如何監視 Azure Redis 快取](cache-how-to-monitor.md)
