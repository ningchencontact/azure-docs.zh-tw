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
ms.openlocfilehash: 5a0d02768b0fbd23e33d13c5e5c3fe84a41cdc52
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243649"
---
# <a name="monitor-azure-file-sync"></a>監視 Azure 檔案同步

使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文說明如何使用 Azure 入口網站和 Windows Server 監視您的 Azure 檔案同步部署。

以下是目前可用的監視選項：

## <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，您可以檢視已註冊的伺服器健康情況、伺服器端點健康情況 (同步健康情況) 和計量。

### <a name="storage-sync-service"></a>儲存體同步服務

若要檢視已註冊的伺服器健康情況、伺服器端點健康情況和計量，請在 Azure 入口網站中移至儲存體同步服務。 已註冊的伺服器健康情況可在 [已註冊的伺服器] 刀鋒視窗中檢視。 伺服器端點健康情況可在 [同步群組] 刀鋒視窗中檢視。

已註冊的伺服器健康情況
- 如果已註冊的伺服器處於「線上」狀態，表示伺服器正順利地與服務進行通訊。
- 如果已註冊的伺服器狀態為「顯示為離線」，請確認伺服器上的儲存體同步監視器 (AzureStorageSyncMonitor.exe) 程序正在執行。 如果伺服器位於防火牆或 Proxy 後方，請依據[文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy)設定防火牆或 Proxy。

伺服器端點健康情況
- 入口網站中的伺服器端點健康情況，取決於伺服器上的遙測事件記錄 (識別碼 9102 和 9302) 中所記錄的同步事件。 如果同步工作階段因暫時性錯誤 (例如，已取消的錯誤) 而失敗，只要目前的同步工作階段有所進展，入口網站中的同步仍可能會顯示為狀況良好 (事件識別碼 9302 可用來判斷是否正在套用檔案)。 如需詳細資訊，請參閱下列文件：[同步健康情況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [同步進度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。
- 如果入口網站因同步沒有進展而顯示同步錯誤，請查看[疑難排解文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以取得指引。

度量
- 您可以在儲存體同步服務入口網站中檢視下列計量：

  | 度量名稱 | 說明 | 入口網站刀鋒視窗 | 
  |-|-|-|
  | 同步的位元組 | 傳輸的資料大小 (上傳和下載) | 同步群組、伺服器端點 |
  | 雲端階層處理重新叫用 | 重新叫用的資料大小 | 已註冊的伺服器 |
  | 檔案無法同步 | 無法同步的檔案計數 | 伺服器端點 |
  | 同步的檔案 | 傳輸的檔案計數 (上傳和下載) | 同步群組、伺服器端點 |
  | 伺服器線上狀態 | 從伺服器接收到的活動訊號計數 | 已註冊的伺服器 |

- 若要深入了解，請參閱 [Azure 監視器](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)一節。 

  > [!Note]  
  > 儲存體同步服務入口網站中的圖表時間範圍為 24 小時。 若要檢視不同的時間範圍或維度，請使用 Azure 監視器。


### <a name="azure-monitor"></a>Azure 監視器

使用 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)可監視同步、雲端階層處理和伺服器連線。 依預設會啟用 Azure 檔案同步的計量，並且每 15 分鐘傳送至 Azure 監視器一次。

若要在 Azure 監視器中檢視 Azure 檔案同步的計量，請選取儲存體同步服務資源類型。

以下是 Azure 監視器中提供的 Azure 檔案同步計量：

| 度量名稱 | 說明 |
|-|-|
| 同步的位元組 | 傳輸的資料大小 (上傳和下載)。<br><br>單位：位元組<br>彙總類型：總和<br>適用維度：伺服器端點名稱、同步方向、同步群組名稱 |
| 雲端階層處理重新叫用 | 重新叫用的資料大小。<br><br>單位：位元組<br>彙總類型：總和<br>適用的維度：伺服器名稱 |
| 檔案無法同步 | 無法同步的檔案計數。<br><br>單位：Count<br>彙總類型：總和<br>適用維度：伺服器端點名稱、同步方向、同步群組名稱 |
| 同步的檔案 | 傳輸的檔案計數 (上傳和下載)。<br><br>單位：Count<br>彙總類型：總和<br>適用維度：伺服器端點名稱、同步方向、同步群組名稱 |
| 伺服器線上狀態 | 從伺服器接收到的活動訊號計數。<br><br>單位：Count<br>彙總類型：最大值<br>適用的維度：伺服器名稱 |
| 同步工作階段結果 | 同步工作階段結果 (1=成功的同步工作階段；0=失敗的同步工作階段)<br><br>單位：Count<br>彙總類型：最大值<br>適用維度：伺服器端點名稱、同步方向、同步群組名稱 |

## <a name="windows-server"></a>Windows Server

在 Windows Server 上，您可以檢視雲端階層處理、已註冊的伺服器和同步健康情況。

### <a name="event-logs"></a>事件記錄檔

使用伺服器上的遙測事件記錄，可監視已註冊的伺服器、同步、和雲端階層處理健康情況。 遙測事件記錄位於事件檢視器中的 Applications and Services\Microsoft\FileSync\Agent 下。

同步健康情況
- 同步工作階段完成後，會記錄事件識別碼 9102。 此事件可用來判斷同步工作階段是否成功完成 (HResult = 0)，以及是否有個別項目的同步錯誤。 如需詳細資訊，請參閱下列文件：[同步健康情況](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [個別項目錯誤](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。

  > [!Note]  
  > 有時候，同步工作階段會完全失敗，或是有非零的 PerItemErrorCount 但仍繼續進行，而有部分檔案成功同步。 這可以從 [已套用] 欄位中看出 (AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes)，在此處您可以了解工作階段成功執行的程度。 如果您發現資料列中有多個同步工作階段雖然失敗，但 [已套用] 計數卻逐漸增加，您即應在建立支援票證之前，保留足夠的時間來重新嘗試同步。

- 如果有使用中的同步工作階段，則會每隔 5 到 10 分鐘記錄事件識別碼 9302 一次。 此事件可用來判斷目前的同步工作階段是否有所進展 (AppliedItemCount > 0)。 如果同步處理沒有進展，則同步工作階段最後應會失敗，並且會記錄包含錯誤的事件識別碼 9102。 如需詳細資訊，請參閱下列文件：[同步進度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

已註冊的伺服器健康情況
- 當伺服器查詢服務中的作業時，將會每 30 秒記錄事件識別碼 9301 一次。 如果 GetNextJob 完成，且狀態 = 0，表示伺服器能夠與服務通訊。 如果 GetNextJob 完成但發生錯誤，請查看[疑難排解文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以取得指引。

雲端階層處理健康情況
- 若要監視伺服器上的分層活動，請在遙測事件記錄中使用事件識別碼 9003、9016 和 9029 (位於事件檢視器中的 Applications and Services\Microsoft\FileSync\Agent 下)。

  - 事件識別碼 9003 會提供伺服器端點的錯誤分布。 例如錯誤總數、錯誤碼等。請注意，系統會為每個錯誤碼記錄一個事件。
  - 事件識別碼 9016 會提供磁碟區的建立映像結果。 例如可用空間百分比、在工作階段中建立映像的檔案數目、無法建立映像的檔案數目等。
  - 事件識別碼 9029 會提供伺服器端點的虛像化工作階段資訊。 例如，在工作階段中嘗試的檔案數目、在工作階段中分層的檔案數目、已分層的檔案數目等。
  
- 若要監視伺服器上的回收活動，請在遙測事件記錄中使用事件識別碼 9005、9006、9009 和 9059 (位於事件檢視器中的 Applications and Services\Microsoft\FileSync\Agent 下)。

  - 事件識別碼 9005 會提供伺服器端點的回收可靠性。 例如，存取的唯一檔案總數、存取失敗的唯一檔案總數等。
  - 事件識別碼 9006 會提供伺服器端點的回收錯誤分布。 例如失敗的要求總數、錯誤碼等。請注意，系統會為每個錯誤碼記錄一個事件。
  - 事件識別碼 9009 會提供伺服器端點的回收工作階段資訊。 例如，DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed 等。
  - 事件識別碼 9059 會提供伺服器端點的應用程式回收分布。 例如，ShareId、應用程式名稱和 TotalEgressNetworkBytes。

### <a name="performance-counters"></a>效能計數器

使用伺服器上的 Azure 檔案同步效能計數器，可監視同步活動。

若要檢視伺服器上的 Azure 檔案同步效能計數器，請啟動效能監視器 (Perfmon.exe)；您可以在「傳輸的 AFS 位元組」和「AFS 同步作業」物件下找到這些計數器。

以下是效能監視器中為 Azure 檔案同步提供的效能計數器：

| 效能物件\計數器名稱 | 說明 |
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
