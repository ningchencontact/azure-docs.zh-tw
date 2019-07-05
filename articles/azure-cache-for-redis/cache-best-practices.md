---
title: Redis Azure 快取的最佳作法
description: 了解如何使用 Redis 遵循這些最佳作法，有效地使用 Azure 快取。
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452458"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Redis Azure 快取的最佳作法 
您可以藉由遵循這些最佳作法，協助最大化效能和成本效益用於 Azure 快取的 Redis 執行個體。

## <a name="configuration-and-concepts"></a>組態和概念
 * **針對生產環境系統中使用標準或進階層。**  基本層是單一節點系統與任何資料複寫並不提供 SLA。 此外，請至少使用 C1 快取。  C0 快取是適用於簡單的開發/測試案例中，因為它們有共用的 CPU 核心、 記憶體少，而且容易發生 「 吵雜鄰居 」 問題。

 * **請記住，Redis 記憶體中的資料存放區。**  [這篇文章](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)概述一些可能發生資料遺失的案例。

 * **開發您的系統，使它可以處理連線短暫中斷**[修補和容錯移轉因為](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)。

 * **設定您[maxmemory 保留設定](cache-configure.md#maxmemory-policy-and-maxmemory-reserved)改善系統回應能力**記憶體壓力的情況下。  此設定是大量寫入工作負載，或如果您在 Redis 中儲存較大的值 （100 KB 或以上） 特別重要。  我會建議您從您的快取大小的 10%，然後如果您具有大量寫入負載增加。 請參閱[一些考量](cache-how-to-troubleshoot.md#considerations-for-memory-reservations)時選取的值。

 * **較小的值與最 redis works**，因此請考慮切分成較大的資料載入多個索引鍵。  在 [此 Redis 討論](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)，，您應該仔細考慮，會列出一些考量。  閱讀 [這篇文章](cache-how-to-troubleshoot.md#large-requestresponse-size) 以了解較大值所造成的範例問題。

 * **在相同的區域中，找出您的快取執行個體和您的應用程式。**  連接到不同區域中的快取可大幅增加延遲，並減少可靠性。  雖然您可以連接從 Azure 外部，但不建議*特別是在做為快取使用 Redis*。  如果您使用 Redis 做只是索引鍵/值存放區，延遲可能不是主要考量。 

 * **重複使用連線**-建立新的連線成本並增加延遲，因此重複使用最大的連線。 如果您選擇建立新的連線，請務必關閉舊的連線後再發行 （甚至是以.NET 或 Java 等的 managed 的記憶體語言）。

 * **設定您要使用的用戶端程式庫*連接逾時*至少 15 秒**，讓連接的系統時間甚至更高的 CPU 情況下。  小型的連接逾時值，並不保證會在該時間範圍內建立的連線。  如果某項目傳送錯誤 （用戶端很高的 CPU、 高的伺服器 CPU 等等），則簡短的連接逾時值，就會導致連接嘗試失敗。 此行為通常可讓不正確的情況更糟。  而不是可幫助，較短的逾時值會加劇強制系統重新啟動的嘗試重新連線，程序的問題，可能會導致*connect]-> [失敗]-> [重試*迴圈。 我們通常建議您保留您的連線逾時 15 秒或更高版本。 最好是讓您比，讓它快速容錯 15 或 20 秒之後，成功的連線嘗試才能再試一次。 這類重試迴圈可能會導致更久，如果您只是讓系統一開始會再接受比您中斷。  
     > [!NOTE]
     > 本指南旨在*連線嘗試*和您願意等候的時間不相關*作業*例如 GET 或 SET 方法完成。
 

 * **避免昂貴的命令**-有些 redis 作業，例如[KEYS 命令](https://redis.io/commands/keys)，會*非常*昂貴，應該予以避免。  如需詳細資訊，請參閱[昂貴的命令相關的一些考量](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>記憶體管理
有幾件事，您可能要考慮的 Redis 伺服器執行個體中的記憶體使用量的相關。  以下是一些：

 * **選擇[收回原則](https://redis.io/topics/lru-cache)，適用於您的應用程式。**  Azure Redis 的預設原則*volatile lru*，這表示有 TTL 的金鑰值組的內容將會收回符合資格。  如果沒有索引鍵有 TTL 值，系統將不會收回任何金鑰。  如果您想要系統以允許任何金鑰被收回，如果記憶體不足的壓力，則您可能要考慮*allkeys lru*原則。

 * **設定您的金鑰的到期值。**  這會移除金鑰主動而不是等到沒有記憶體不足的壓力。  當收回會開始作用，因為記憶體不足的壓力時，卻可能造成額外的負載在伺服器上。  如需詳細資訊，請參閱文件[過期](https://redis.io/commands/expire)並[ExpireAt](https://redis.io/commands/expireat)命令。
 
## <a name="client-library-specific-guidance"></a>用戶端程式庫中的特定指引
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-應該使用哪一個用戶端？](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net 工作階段狀態提供者](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>何時應該安全地重試一次？
不幸的是，沒有任何簡單的答案。  每個應用程式必須決定哪些作業可以重試、 哪些不可安裝。  每個作業都不同的需求和索引鍵間的相依性。  以下是一些您可能要考慮的事項：

 * 您可以取得用戶端錯誤，即使 Redis 已成功執行系統要求您執行命令。  例如:
     - 逾時是用戶端的概念。  如果作業已送達伺服器，伺服器會執行命令，即使在用戶端放棄等候。  
     - 當通訊端連線發生錯誤時，它不知道是否伺服器上實際執行的作業。  例如，由伺服器，但用戶端收到回應之前處理要求之後，就會發生連接錯誤。
 *  如何沒有我的應用程式有什麼反應我不小心執行兩次相同的作業？  比方說，要是我遞增整數，兩次而非一次嗎？  我的應用程式將寫入至相同的索引鍵從多個位置嗎？  如果我的重試邏輯會覆寫的我的應用程式的某個其他部分所設定的值？

如果您想要測試您的程式碼錯誤的情況下的運作方式，請考慮使用[重新啟動功能](cache-administration.md#reboot)。 這可讓您查看連線短暫中斷對您的應用程式的影響。

## <a name="performance-testing"></a>效能測試
 * **開始使用`redis-benchmark.exe`** 瞭解可能的輸送量/延遲之前撰寫您自己的效能測試。  Redis 基準文件可以是[這裡找到](http://redis.io/topics/benchmarks)。  請注意，redis-benchmark 不支援 SSL，因此您必須[啟用非 SSL 連接埠，透過入口網站](cache-configure.md#access-ports)執行測試之前。  [Windows 相容的 benchmark.exe 版本可以在這裡找到](https://github.com/MSOpenTech/redis/releases)
 * 用戶端用於測試的 VM 應**相同的區域中**與您的 Redis 快取執行個體。
 * **我們建議使用 Dv2 VM 系列**用戶端因為有更好的硬體，將會提供最佳的結果。
 * 請確定您使用的 VM 具有用戶端 **程度的計算和頻寬*與所測試的快取。 
 * **啟用 VRSS**如果您在 Windows 上的用戶端電腦上。  [參閱此處了解詳細資訊](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx)。  範例 powershell 指令碼：
     >PowerShell-ExecutionPolicy Unrestricted 啟用 NetAdapterRSS-名稱 (Get-netadapter)。名稱 
     
 * **請考慮使用進階層 Redis 執行個體**。  這些快取大小會有更好的網路延遲和輸送量，因為它們更好的硬體上執行的 CPU 和網路。
 
     > [!NOTE]
     > 我們觀察到的效能結果[發佈](cache-faq.md#azure-cache-for-redis-performance)供您參考。   此外，請注意，SSL/TLS 會將新增一些額外負荷，因此您可能會收到不同的延遲和/或輸送量如果您使用傳輸加密。
 
### <a name="redis-benchmark-examples"></a>Redis 基準範例
**前置測試設定**:這會延遲及輸送量測試下面所列的命令所需的資料準備快取執行個體。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**若要測試延遲**:這會測試使用 1k 承載的 GET 要求。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**若要測試的輸送量：** 這會將已傳送 GET 要求使用 1k 承載。
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t  GET -n 1000000 -d 1024 -P 50  -c 50
