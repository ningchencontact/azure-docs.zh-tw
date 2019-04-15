---
title: 監視 Azure 檔案同步 | Microsoft Docs
description: 如何監視 Azure 檔案同步。
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 4ae17249903f317e7a75a3e6bc7c03292021c96a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534628"
---
# <a name="monitor-azure-file-sync"></a>監視 Azure 檔案同步

使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文說明如何使用 Azure 入口網站和 Windows Server 來監視您的 Azure 檔案同步部署。

目前提供下列監視選項。

## <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，您可以檢視已註冊的伺服器健康情況、伺服器端點健康情況 (同步健康情況) 和計量。

### <a name="storage-sync-service"></a>儲存體同步服務

若要檢視已註冊的伺服器健全狀況、 伺服器端點健康情況和計量，請移至儲存體同步服務，在 Azure 入口網站中。 您可以檢視已註冊的伺服器中的健康情況**已註冊的伺服器**中的刀鋒視窗和伺服器端點健全狀況**同步處理群組**刀鋒視窗。

已註冊的伺服器健全狀況：

- 如果**已註冊的伺服器**狀態**線上**，與服務成功通訊伺服器。
- 如果**已註冊的伺服器**狀態**離線出現**，確認儲存體同步處理監視器 (AzureStorageSyncMonitor.exe) 處理序伺服器上的正在執行。 如果伺服器位於防火牆或 proxy 後方，請參閱[這篇文章](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)設定防火牆和 proxy。

伺服器端點的健康狀態：

- 入口網站中的伺服器端點健康情況，取決於伺服器上的遙測事件記錄 (識別碼 9102 和 9302) 中所記錄的同步事件。 如果因為暫時性的錯誤，所以的同步工作階段失敗，例如錯誤取消，同步處理仍可能會顯示在入口網站中狀況良好，只要目前的同步處理工作階段有所進展。 事件識別碼 9302 用來判斷是否套用檔案中。 如需詳細資訊，請參閱 <<c0> [ 同步處理健全狀況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)並[同步進度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。
- 如果入口網站會顯示同步處理錯誤，因為同步而沒有進度，請參閱[疑難排解文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)指導方針。

度量：

- 您可以在儲存體同步服務入口網站中檢視下列計量：

  | 度量名稱 | 描述 | 刀鋒視窗中的名稱 |
  |-|-|-|
  | 同步的位元組 | 傳輸的資料大小 (上傳和下載) | 同步群組、伺服器端點 |
  | 雲端階層處理重新叫用 | 重新叫用的資料大小 | 已註冊的伺服器 |
  | 檔案無法同步 | 無法同步的檔案計數 | 伺服器端點 |
  | 同步的檔案 | 傳輸的檔案計數 (上傳和下載) | 同步群組、伺服器端點 |
  | 伺服器線上狀態 | 從伺服器接收到的活動訊號計數 | 已註冊的伺服器 |

- 若要進一步了解，請參閱[Azure 監視器](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > 儲存體同步服務入口網站中的圖表時間範圍為 24 小時。 若要檢視不同的時間範圍或維度，請使用 Azure 監視器。

### <a name="azure-monitor"></a>Azure 監視器

使用 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)可監視同步、雲端階層處理和伺服器連線。 依預設會啟用 Azure 檔案同步的計量，並且每 15 分鐘傳送至 Azure 監視器一次。

若要在 Azure 監視器中檢視 Azure 檔案同步的度量資訊，請選取**儲存體同步服務**資源類型。

以下是 Azure 監視器中提供的 Azure 檔案同步計量：

| 度量名稱 | 描述 |
|-|-|
| 同步的位元組 | 傳輸的資料大小 (上傳和下載)。<br><br>單位：位元組<br>彙總類型：總和<br>適用維度：伺服器端點名稱、同步方向、同步群組名稱 |
| 雲端階層處理重新叫用 | 重新叫用的資料大小。<br><br>單位：位元組<br>彙總類型：總和<br>適用的維度：伺服器名稱 |
| 檔案無法同步 | 無法同步的檔案計數。<br><br>單位：計數<br>彙總類型：總和<br>適用維度：伺服器端點名稱、同步方向、同步群組名稱 |
| 同步的檔案 | 傳輸的檔案計數 (上傳和下載)。<br><br>單位：計數<br>彙總類型：總和<br>適用維度：伺服器端點名稱、同步方向、同步群組名稱 |
| 伺服器線上狀態 | 從伺服器接收到的活動訊號計數。<br><br>單位：計數<br>彙總類型：最大值<br>適用的維度：伺服器名稱 |
| 同步工作階段結果 | 同步工作階段結果 (1=成功的同步工作階段；0=失敗的同步工作階段)<br><br>單位：計數<br>彙總類型：最大值<br>適用維度：伺服器端點名稱、同步方向、同步群組名稱 |

## <a name="windows-server"></a>Windows Server

在 Windows 伺服器上，您可以檢視雲端階層處理，已註冊的伺服器，並同步處理健全狀況。

### <a name="event-logs"></a>事件記錄

使用伺服器上的遙測事件記錄，可監視已註冊的伺服器、同步、和雲端階層處理健康情況。 遙測事件記錄檔位於事件檢視器底下*Applications and Services\Microsoft\FileSync\Agent*。

同步處理健全狀況：

- 同步工作階段完成之後，會記錄事件識別碼 9102。 使用這個事件來判斷是否成功同步處理工作階段 (**HResult = 0**) 以及是否有每個項目同步處理錯誤。 如需詳細資訊，請參閱 <<c0> [ 同步處理健全狀況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)並[每個項目錯誤](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)文件。

  > [!Note]  
  > 有時整體同步處理工作階段失敗，或有非零 PerItemErrorCount。 不過，它們仍繼續往前進行，而且某些檔案已成功同步。 您可以看到這在例如 AppliedFileCount、 AppliedDirCount、 AppliedTombstoneCount 和 AppliedSizeBytes 套用的欄位。 這些欄位會告訴您工作階段中有多少成功。 如果您看到多個資料列，在失敗的同步處理工作階段，而且還不斷增加的已套用的計數，讓同步處理時間再試一次您開啟支援票證之前。

- 如果有使用中的同步工作階段，則會每隔 5 到 10 分鐘記錄事件識別碼 9302 一次。 使用這個事件來判斷是否目前的同步處理工作階段是否有所進展 (**AppliedItemCount > 0**)。 如果同步處理而沒有進度，同步工作階段最後應會失敗，並將記錄事件識別碼 9102，發生錯誤。 如需詳細資訊，請參閱 <<c0> [ 同步處理進行文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已註冊的伺服器健全狀況：

- 當伺服器查詢服務中的作業時，將會每 30 秒記錄事件識別碼 9301 一次。 如果完成 GetNextJob**狀態 = 0**，伺服器是否能夠與服務通訊。 如果 GetNextJob 完成但發生錯誤，請檢查[疑難排解文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)指導方針。

雲端階層處理的健康情況：

- 若要監視伺服器上的階層處理活動，使用 [事件識別碼 9003，時間 9016 和遙測事件記錄檔，位於事件檢視器] 中的 9029 *Applications and Services\Microsoft\FileSync\Agent*。

  - 事件識別碼 9003 會提供伺服器端點的錯誤分布。 例如︰ErrorCode 和總錯誤計數。 每個錯誤的程式碼，會記錄一個事件。
  - 事件識別碼 9016 會提供磁碟區的建立映像結果。 例如︰可用空間百分比，工作階段中，建立映像的檔案數目以及無法準刪除的檔案數目。
  - 事件識別碼 9029 會提供伺服器端點的虛像化工作階段資訊。 例如︰嘗試在工作階段中，檔案數目的檔案數目的階層式工作階段中，並已分層的檔案數目。
  
- 若要監視伺服器上的重新叫用活動，使用 [事件識別碼 9005，9006、 9009 和遙測事件記錄檔，位於事件檢視器] 中的 9059 *Applications and Services\Microsoft\FileSync\Agent*。

  - 事件識別碼 9005 會提供伺服器端點的回收可靠性。 例如︰總唯一的檔案存取，且總唯一的檔案與失敗的存取。
  - 事件識別碼 9006 會提供伺服器端點的回收錯誤分布。 例如︰總計失敗的要求或錯誤碼。 每個錯誤的程式碼，會記錄一個事件。
  - 事件識別碼 9009 會提供伺服器端點的回收工作階段資訊。 例如︰DurationSeconds、 CountFilesRecallSucceeded 和 CountFilesRecallFailed。
  - 事件識別碼 9059 會提供伺服器端點的應用程式回收分布。 例如︰ShareId、 應用程式名稱和 TotalEgressNetworkBytes。

### <a name="performance-counters"></a>效能計數器

使用伺服器上的 Azure 檔案同步效能計數器，可監視同步活動。

若要檢視伺服器上的 Azure 檔案同步效能計數器，請開啟效能監視器 (Perfmon.exe)。 您可以找到的計數器底下**AFS 傳輸位元組**並**AFS 同步處理作業**物件。

以下是效能監視器中為 Azure 檔案同步提供的效能計數器：

| 效能物件\計數器名稱 | 描述 |
|-|-|
| 傳輸的 AFS 位元組\每秒下載的位元組 | 每秒下載的位元組數。 |
| 傳輸的 AFS 位元組\每秒上傳的位元組 | 每秒上傳的位元組數。 |
| 傳輸的 AFS 位元組\每秒的位元組總數 | 每秒的位元組總數 (上傳和下載)。 |
| AFS 同步作業\每秒下載的同步檔案 | 每秒下載的檔案數。 |
| AFS 同步作業\每秒上傳的同步檔案 | 每秒上傳的檔案數。 |
| AFS 同步作業\每秒的同步檔案作業總數 | 同步的檔案總數 (上傳和下載)。 |

## <a name="next-steps"></a>後續步驟
- [規劃 Azure 檔案同步部署](storage-sync-files-planning.md)
- [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
- [針對 Azure 檔案同步進行移難排解](storage-sync-files-troubleshoot.md)
- [Azure 檔案服務常見問題集](storage-files-faq.md)
