---
title: Azure 檔案效能疑難排解指南
description: 已知的進階 Azure 檔案共用 （預覽） 與相關聯的因應措施的效能問題。
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 5ae0bb736a7cc0bbc38df5905abc5d8a71f60eb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190043"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>針對 Azure 檔案服務的效能問題進行疑難排解

本文列出一些常見的問題與 premium Azure 檔案共用 （預覽）。 它提供可能的原因和因應措施時遇到這些問題。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延遲、 低的輸送量和一般的效能問題

### <a name="cause-1-share-experiencing-throttling"></a>原因 1：共用發生節流

在共用上的預設配額是 100 GiB，提供 100 基準 IOPS （可能高達 300 載到 1 小時）。 如需有關佈建和其 IOPS 的關聯性的詳細資訊，請參閱 <<c0> [ 佈建共用](storage-files-planning.md#provisioned-shares)規劃指南的區段。

若要確認您的共用會進行節流處理，您可以在入口網站中利用 Azure 計量。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取 **所有的服務**，然後搜尋**計量**。

1. 選取 [計量]  。

1. 選取儲存體帳戶和資源。

1. 選取 **檔案**為度量的命名空間。

1. 選取 **交易**做為計量。

1. 新增的篩選條件**ResponseType**和任何要求是否有回應碼的核取**SuccessWithThrottling**。

![進階檔案共用的度量選項](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>解決方法

- 增加共用佈建的容量，藉由共用上指定更高的配額。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因 2：工作負載過重的中繼資料/命名空間

如果您的要求，就大部分的中繼資料中心 （例如 createfile/openfile/closefile/queryinfo/querydirectory) 延遲將會更糟，相較於讀寫作業。

若要確認是否您的要求的大部分中繼資料中心，您可以使用相同的步驟，如上所述。 而不是新增的篩選器除外**ResponseType**，新增的篩選條件**API 名稱**。

![篩選您的計量中的 API 名稱](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>因應措施

- 請檢查是否可以減少中繼資料作業修改應用程式。

### <a name="cause-3-single-threaded-application"></a>原因 3：單一執行緒應用程式

如果客戶正在使用的應用程式是單一執行緒，這導致大幅降低 IOPS/輸送量比佈建的共用大小為基礎的最大可能。

### <a name="solution"></a>解決方法

- 增加應用程式平行處理原則的執行緒數目。
- 切換至 應用程式平行處理原則的可能。 比方說，對於複製作業，客戶可以使用 AzCopy 或 RoboCopy 從 Windows 用戶端或**平行**Linux 用戶端上的命令。

## <a name="very-high-latency-for-requests"></a>要求非常高的延遲

### <a name="cause"></a>原因

用戶端 VM 可以位於與 premium 檔案共用不同的區域。

### <a name="solution"></a>解決方法

- 從 VM 位於與 premium 檔案共用相同的區域中執行應用程式。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>用戶端無法達到網路支援的最大輸送量

其中一個可能原因是無權 fo SMB 多重通道支援。 目前，Azure 檔案共用只支援單一通道，因此從用戶端 VM 只有一個連線到伺服器。 此單一連線會限制單一核心 VM，在用戶端上，因此從 VM 可達成的最大輸送量受限於單一核心。

### <a name="workaround"></a>因應措施

- 取得具有更大的核心的 VM，可能有助於改善輸送量。
- 從多個 Vm 執行用戶端應用程式將會增加輸送量。
- 可能的話，請使用 REST Api。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>在 Linux 用戶端上的輸送量會大幅降低，相較於 Windows 用戶端。

### <a name="cause"></a>原因

這是在 Linux 上的 SMB 用戶端實作的已知的問題。

### <a name="workaround"></a>因應措施

- 負載分散到多個 Vm
- 在相同的 vm，使用 具有多個掛接點**nosharesock**選項，然後展開的工作負荷都這些掛接點。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>與廣泛的開啟/關閉作業有關的中繼資料大量工作負載的高延遲。

### <a name="cause"></a>原因

不支援的目錄租用。

### <a name="workaround"></a>因應措施

- 可能的話，請在短時間內避免過多的開頭/結尾控制代碼上相同的目錄。
- 針對 Linux Vm，增加的目錄項目快取逾時指定**actimeo =<sec>** 做為掛接選項。 根據預設，它會是一秒，因此較大的值，例如三或五個可能有幫助。
- 適用於 Linux Vm 升級 4.20 或更高的核心。

## <a name="low-iops-on-centosrhel"></a>在 CentOS/RHEL 上的低 IOPS

### <a name="cause"></a>原因

在 CentOS/RHEL 上不支援大於一的 IO 深度。

### <a name="workaround"></a>因應措施

- 升級至 CentOS 8 / RHEL 8。
- 變更到 Ubuntu。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS 的抖動/saw tooth 模式

### <a name="cause"></a>原因

用戶端應用程式持續超過基準 IOPS。 目前沒有任何服務端的平滑處理的要求負載，因此如果用戶端超過基準 IOPS，它會受到節流管制服務。 該節流，可能會導致發生抖動/saw tooth IOPS 模式用戶端。 在此情況下，平均用戶端所達到的 IOPS 可能低於基準的 IOPS。

### <a name="workaround"></a>因應措施

- 使共用不會不會進行節流，請從用戶端應用程式中，減少的要求負載。
- 增加配額的共用，讓共用不會不會進行節流。

## <a name="excessive-directoryopendirectoryclose-calls"></a>過多的 DirectoryOpen/DirectoryClose 呼叫

### <a name="cause"></a>原因

如果 DirectoryOpen/DirectoryClose 呼叫數目之間的最上層的 API 呼叫，而且您不預期用戶端來進行許多呼叫，它可能 Azure 的用戶端 VM 上安裝防毒軟體發生問題。

### <a name="workaround"></a>因應措施

- 此問題的修正位於[年 4 月平台更新為 Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)。

## <a name="file-creation-is-slower-than-expected"></a>建立檔案會比預期還要慢

### <a name="cause"></a>原因

需要建立大量檔案的工作負載不會看到進階檔案共用的效能與標準檔案共用之間大幅差異。

### <a name="workaround"></a>因應措施

- 無。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>從 Windows 8.1 或 Server 2012 R2 的效能變慢

### <a name="cause"></a>原因

高於預期存取 Azure 檔案的 IO 密集型工作負載的延遲。

### <a name="workaround"></a>因應措施

- 安裝的可用[hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。