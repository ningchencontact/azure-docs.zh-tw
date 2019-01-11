---
title: 檢查是否有集區和節點錯誤 - Azure Batch
description: 建立集區和節點時所要檢查的錯誤及如何加以避免
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 4e1e645c25d2f1e49e222e39ecd719a414e1404e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790464"
---
# <a name="check-for-pool-and-node-errors"></a>檢查是否有集區和節點錯誤

當您建立和管理 Azure Batch 集區時，某些作業將會立即執行。 不過，某些作業則會以非同步方式在背景中執行。 這些作業可能需要數分鐘才能完成。

要偵測立即執行的作業是否失敗並不難，因為 API、CLI 或 UI 會立即傳回所有失敗。

本文說明可能為集區和集區節點執行的背景作業。 文中將闡明如何偵測和避免失敗。

## <a name="pool-errors"></a>集區錯誤

### <a name="resize-timeout-or-failure"></a>調整逾時或失敗

在建立新集區或調整現有集區的大小時，您會指定目標節點數目。  此作業會立即完成，但新節點的實際配置或現有節點的移除可能需要數分鐘才能完成。  您可以在[建立](https://docs.microsoft.com/rest/api/batchservice/pool/add)或[調整大小](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 中指定調整逾時。 如果 Batch 無法在調整逾時期間內取得目標節點數目，即會停止作業。 集區會進入穩定狀態，並報告調整大小錯誤。

最新評估的 [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) 屬性會報告調整逾時，並列出已發生的任何錯誤。

調整逾時的常見原因包括：

- 調整逾時太短
  - 在一般情況下，預設的逾時 15 分鐘通常即足以進行集區節點的配置或移除。
  - 如果您將配置大量的節點，建議您將調整逾時設為 30 分鐘。 例如，當您要從 Azure Marketplace 映像將大小調整為 1000 個以上的節點，或從自訂 VM 映像調整為 300 個以上的節點。
- 核心配額不足
  - Batch 帳戶可配置給所有集區的核心數目有其限制。 達到該配額之後，Batch 就會停止配置節點。 您[可以增加](https://docs.microsoft.com/azure/batch/batch-quota-limit)核心配額，使 Batch 能夠配置更多節點。
- 當[集區位於虛擬網路中](https://docs.microsoft.com/azure/batch/batch-virtual-network)時，子網路 IP 不足
  - 虛擬網路子網路必須有足夠的未指派 IP 位址可配置給每個要求的集區節點。 否則，就會無法建立節點。
- 當[集區位於虛擬網路中](https://docs.microsoft.com/azure/batch/batch-virtual-network)時，資源不足
  - 您可以在與 Batch 帳戶相同的訂用帳戶中建立資源，例如負載平衡器、公用 IP 和網路安全性群組。 請確認訂用帳戶配額足夠供這些資源使用。
- 使用自訂 VM 映像的大型集區
  - 使用自訂 VM 映像的大型集區可能需要較長的時間進行配置，因此可能會發生調整逾時。  請參閱[使用自訂映像來建立虛擬機器的集區](https://docs.microsoft.com/azure/batch/batch-custom-images)，以取得限制和組態的相關建議。

### <a name="automatic-scaling-failures"></a>自動調整失敗

您也可以設定 Azure Batch，使其自動調整集區中的節點數目。 您必須為[集區的自動調整公式](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)定義參數。 Batch 服務會使用此公式定期評估集區中的節點數目，並設定新的目標數目。 以下是可能偵測到的問題類型：

- 自動調整評估失敗。
- 產生的調整大小作業失敗並逾時。
- 自動調整公式的問題會導致不正確的節點目標值。 調整大小可能會成功或逾時。

您可以使用 [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) 屬性取得上次自動調整評估的相關資訊。 此屬性會報告評估時間、值和結果，以及任何效能錯誤。

[集區調整大小完成事件](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)會擷取所有評估的相關資訊。

### <a name="delete"></a>刪除

當您刪除包含節點的集區時，Batch 會先刪除節點。 然後它會刪除集區物件本身。 刪除這些節點可能需要幾分鐘的時間。

在刪除過程中，Batch 會將[集區狀態](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)設為**刪除中**。 呼叫端應用程式可以使用 **state** 和 **stateTransitionTime** 屬性來偵測集區刪除作業是否耗費太多時間。

## <a name="pool-compute-node-errors"></a>集區計算節點錯誤

即使 Batch 在集區中成功配置了節點，仍有各種問題可能導致某些節點狀況不良和無法使用。 這些節點會產生費用。 請務必偵測問題，以免為無法使用的節點支付費用。

### <a name="start-task-failure"></a>開始工作失敗

您可以為集區指定選擇性的[開始工作](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)。 與任何工作一樣，您可以指定命令列和要從儲存體下載的資源檔案。 每個節點啟動後，系統即會分別執行其「開始工作」。 **waitForSuccess** 屬性會指定 Batch 是否應等到「開始工作」順利完成後，再為節點排定任何工作。

如果您已設定要讓節點等待「開始工作」順利完成，但開始工作卻失敗，該如何處理？ 在此情況下，節點將無法使用，但仍會產生費用。

您可以使用最上層 [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) 節點屬性的 [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) 和 [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) 屬性，來偵測失敗的「開始工作」。

失敗的開始工作也會導致 Batch 將節點[狀態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)設為 **starttaskfailed**，但前提是您已將 **waitForSuccess** 設為 **true**。

與任何工作一樣，開始工作失敗的原因可能有許多個。  若要進行疑難排解，請檢查 stdout、stderr 及任何進一步的工作特定記錄檔。

### <a name="application-package-download-failure"></a>應用程式套件下載失敗

您可以為集區指定一或多個應用程式套件。 Batch 會將指定的套件檔案下載至每個節點，並在節點啟動之後、排定工作之前將這些檔案解壓縮。 將開始工作命令列與應用程式套件搭配使用是常見的做法。 例如，用來將檔案複製到不同的位置或執行安裝程式。

節點的 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 屬性會報告應用程式套件的下載和解壓縮失敗。 Batch 會將節點狀態設為**無法使用**。

### <a name="node-in-unusable-state"></a>處於無法使用狀態的節點

Azure Batch 將[節點狀態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)設為**無法使用**的原因有很多。 當節點狀態設為**無法使用**時，將無法為節點排定工作，但節點仍將產生費用。

Batch 一律會嘗試復原無法使用的節點，但視原因而定，可能可以進行復原，也可能無法進行復原。

如果 Batch 可判斷出原因，節點的 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 屬性會加以報告。

導致節點**無法使用**的其他原因包括：

- 自訂 VM 映像無效。 例如，未正確備妥的映像。
- VM 因基礎結構失敗或低層級升級而移動。 Batch 會復原節點。

### <a name="node-agent-log-files"></a>節點代理程式記錄檔

在每個集區節點上執行的 Batch 代理程式程序可提供記錄檔，在您需要連絡支援人員來解決集區節點問題時，可能有所幫助。 節點的記錄檔可透過 Azure 入口網站、Batch Explorer 或 [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) 來上傳。 上傳並儲存記錄檔，將對您有幫助。 其後，您可以刪除節點或集區，以節省執行節點的成本。

## <a name="next-steps"></a>後續步驟

請確認您已設定應用程式以實作完整的錯誤檢查，特別是針對非同步作業。 這可能是能否立即偵測並診斷問題的關鍵。
