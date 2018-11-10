---
title: 診斷 Azure Cosmos DB Java Async SDK 並進行疑難排解 | Microsoft Docs
description: 使用像是用戶端記錄的功能及其他協力廠商工具，針對 Azure Cosmos DB 問題進行識別、診斷及疑難排解。
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshoot
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: ef1d2d0751bf1b1a7ee88fbf37e44e6316dee8f8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249859"
---
# <a name="troubleshooting-issues-when-using-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>針對搭配 Azure Cosmos DB SQL API 帳戶使用 Java Async SDK 時所發生的問題進行疑難排解
此文章涵蓋搭配 Azure Cosmos DB SQL API 帳戶使用 [Java Async SDK](sql-api-sdk-async-java.md) 時的常見問題、因應措施、診斷步驟與工具。
Java Async SDK 提供用戶端邏輯表示法來存取 Azure Cosmos DB SQL API。 此文章所說明的工具和方法，可以在您遇到任何問題時提供協助。

從此清單開始：
    * 查看此文章的[常見問題和因應措施]一節。
    * 我們的 SDK 是 [GitHub 上的開放原始碼](https://github.com/Azure/azure-cosmosdb-java) \(英文\)，而且有個我們會主動監視的[問題區段](https://github.com/Azure/azure-cosmosdb-java/issues) \(英文\)。 檢查您是否可以找到任何含有已提出因應措施的類似問題。
    * 檢閱[效能祕訣](performance-tips-async-java.md)並遵循建議的作法。
    * 遵循此文章的其餘部分，如果找不到解決方案，請提出 [GitHub 問題](https://github.com/Azure/azure-cosmosdb-java/issues)。

## <a name="common-issues-workarounds"></a>常見問題和因應措施

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>網路問題, Netty 讀取逾時失敗, 低輸送量, 高延遲

#### <a name="general-suggestions"></a>一般建議
* 確定應用程式會在與您 Cosmos DB 帳戶相同的區域上執行。 
* 在應用程式執行所在的主機上檢查 CPU 使用量。 如果 CPU 使用量為 90% 或更多，請考慮在具有較高設定的主機上執行您的應用程式，或將負載散發到更多電腦上。

#### <a name="connection-throttling"></a>連線節流
連線節流的發生原因可能是因為[主機電腦上的連線限制]或 [Azure SNAT (PAT) 連接埠耗盡]：

##### <a name="connection-limit-on-host"></a>主機電腦上的連線限制
某些 Linux 系統 (例如 'Red Hat') 具有開啟檔案的總數上限。 Linux 中的通訊端會實作為檔案，因此，這個數字也會限制連線總數。
執行以下命令：

```bash
ulimit -a
```
開啟檔案的數目 ("nofile") 必須夠大 (至少是您連線集區大小的兩倍)。 更詳細地閱讀[效能祕訣](performance-tips-async-java.md)。

##### <a name="snat"></a>Azure SNAT (PAT) 連接埠耗盡

如果您的應用程式部署於 Azure VM 上，預設會使用 [Azure SNAT 連接埠](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)來建立與您 VM 外部之任何端點的連線。 從 VM 到 Cosmos DB 端點所允許的連線數目會受到 [Azure SNAT 設定](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)所限制。

只有在您的 Azure VM 具有私人 IP 位址，而且來自 VM 的程序會嘗試建立與公用 IP 位址的連線時，才會使用 Azure SNAT 連接埠。 因此，有兩種因應措施可避免 Azure SNAT 限制：
    * 將您的 Azure Cosmos DB 服務端點新增至您的 Azure VM VNET 子網路，如[啟用 VNET 服務端點](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)中所述。 啟用服務端點時，已不會將要求從公用 IP 傳送到 Cosmos DB，而是改為傳送 VNET 和子網路身分識別。 如果只允許公用 IP，此變更可能會導致防火牆卸除。 如果您使用防火牆，則在啟用服務端點時，使用 [VNET ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl) 來將子網路新增至防火牆。
    * 將公用 IP 指派給您的 Azure VM。

#### <a name="http-proxy"></a>HTTP Proxy

如果您使用 HttpProxy，請確定您的 HttpProxy 可以支援 SDK `ConnectionPolicy` 中所設定的連線數目。
否則就會遇到連線問題。

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>程式碼編寫模式無效：封鎖 Netty IO 執行緒

SDK 會使用 [Netty](https://netty.io/) \(英文\) IO 程式庫來與 Azure Cosmos DB 服務通訊。 我們具有 Async API，而且會使用 Netty 的非封鎖 IO API。 SDK 的 IO 工作會在 IO Netty 執行緒上執行。 IO Netty 執行緒的數目會設定為與應用程式電腦的 CPU 核心數目相同。 Netty IO 執行緒僅適用於非封鎖的 Netty IO 工作。 SDK 會將其中一個 Netty IO 執行緒上的 API 引動過程結果傳回給應用程式的程式碼。 如果應用程式於 Netty 執行緒上接收到結果之後，會在 Netty 執行緒上執行長時間持續的作業，可能就會導致 SDK 沒有足夠的 IO 執行緒數目來執行其內部 IO 工作。 這類應用程式的程式碼編寫可能導致低輸送量、高延遲及 `io.netty.handler.timeout.ReadTimeoutException` 失敗。 因應措施是在您知道作業將很費時之後切換執行緒。

   例如，下列程式碼片段顯示，如果您在 Netty 執行緒上執行長時間持續的工作 (花費的時間超過數毫秒)，您最終會對沒有 Netty IO 執行緒可處理 IO 工作而感到不安，因此您會得到 ReadTimeoutException：
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // time consuming work. For example:
                        // writing to a file, computationally heavy work, or just sleep
                        // basically anything which takes more than a few milliseconds
                        // doing such operation on the IO netty thread
                        // without a proper scheduler, will cause problems.
                        // The subscriber will get ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //will be io.netty.handler.timeout.ReadTimeoutException
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   因應措施是變更要在其上執行費時工作的執行緒。 為您的應用程式定義排程器的單一執行個體：
   ```java
// have a singleton instance of executor and scheduler
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   每當您需要執行費時的工作 (例如，大量運算工作、封鎖 IO) 時，請使用 `.observeOn(customScheduler)` API，將執行緒切換至您的 `customScheduler` 所提供的背景工作。
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // switches the thread.
        .subscribe(
            // ...
        );
```
使用 `observeOn(customScheduler)`，您就能釋放 Netty IO 執行緒，並切換至您自己由 customScheduler 所提供的自訂執行緒。 此修改將會解決問題，而您將不再發生 `io.netty.handler.timeout.ReadTimeoutException` 失敗。

### <a name="connection-pool-exhausted-issue"></a>連線集區耗盡的問題

`PoolExhaustedException` 是一個用戶端失敗。 如果您經常發生此失敗，那就表示您的應用程式工作負載比 SDK 連線集區可提供的工作負載還要高。 增加連線集區大小或將負載散發到多個應用程式上可能有所幫助。

### <a name="request-rate-too-large"></a>要求率太大
此失敗是伺服器端失敗，表示您耗用了佈建的輸送量，應稍後重試。 如果您經常發生此失敗，請考慮增加集合輸送量。

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>無法連線至 Azure Cosmos DB 模擬器

Cosmos DB 模擬器的 HTTPS 憑證是自我簽署的。 針對要與模擬器搭配運作的 SDK，您應該將模擬器憑證匯入到 Java TrustStore。 如[此處](local-emulator-export-ssl-certificates.md)所述。


## <a name="enable-client-sice-logging"></a>啟用用戶端 SDK 記錄

非同步 Java SDK 會使用 SLF4j 作為記錄外觀，以支援登入到 log4j 和 logback 等熱門記錄架構。

例如，如果您想要使用 log4j 作為記錄架構，請在您的 Java 類別路徑中新增下列程式庫：

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

此外，也會新增 log4j 設定：
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

如需詳細資訊，請檢閱 [sfl4j 記錄手冊](https://www.slf4j.org/manual.html) \(英文\)。

## <a name="netstats"></a>OS 網路統計資料
執行 netstat 命令，以了解有多少個連線處於 `Established` 狀態、`CLOSE_WAIT` 狀態等。

在 Linux上，您可以執行下列命令：
```bash
netstat -nap
```
只篩選出與 Cosmos DB 端點連線的結果。

很顯然地，處於 `Established` 狀態且與 Cosmos DB 端點連線的數目不能大於您所設定連線集區的大小。

如果有許多處於 `CLOSE_WAIT` 狀態且與 Cosmos DB 端點的連線 (例如，超過 1000 個連線)，那就表示連線已快速建立並終止，這樣可能會造成問題。 如需詳細資訊，請檢閱[常見問題和因應措施]一節。

 <!--Anchors-->
[常見問題和因應措施]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[主機電腦上的連線限制]: #connection-limit-on-host
[Azure SNAT (PAT) 連接埠耗盡]: #snat


