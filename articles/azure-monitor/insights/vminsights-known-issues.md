---
title: 適用於 VM 的 Azure 監視器 (預覽) 已知問題 | Microsoft Docs
description: 本文涵蓋適用於 VM 的 Azure 監視器已知問題，Azure 中一個結合了 Azure VM 作業系統健康情況和效能監視的解決方案。 適用於 VM 的 Azure 監視器也會自動探索應用程式元件及與其他資源的相依性，並對應它們之間的通訊。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: b2a60ddee58a7a9c37582085132ee8a1df767cb4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190351"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>適用於 VM 的 Azure 監視器 (預覽) 已知問題

本文涵蓋適用於 VM 的 Azure 監視器已知問題，Azure 中一個結合了 Azure VM 作業系統健康情況和效能監視的解決方案。 

以下為健康情況功能的已知問題：

- 健康情況功能會針對連線到 Log Analytics 工作區的所有 VM 加以啟用。 即使動作在單一 VM 上開始和結束，也是如此。
- 在您使用支援的方法停用對 VM 的監視，並嘗試再次部署它之後，您應該將它部署於相同的工作區中。 如果您使用新的工作區並嘗試檢視該 VM 的健全狀態，它可能會顯示異常行為。
- 如果移除或刪除了 Azure VM，則它會在 VM 清單檢視中顯示三到七天。 此外，按一下已移除或已刪除 VM 的狀態，即會開啟 [健康情況診斷] 檢視，繼而初始載入迴圈。 選取已刪除 VM 的名稱會開啟一個窗格，並顯示一則指出 VM 已刪除的訊息。
- 此版本的健全準則時段和頻率均無法修改。 
- 健全準則無法停用。 
- 部署之後，可能需要一些時間，資料才會顯示於 [Azure 監視器] > [虛擬機器] > [健康情況] 窗格或 [VM 資源] > [Insights] 窗格中。
- 健康情況診斷體驗的更新速度比任何其他檢視還快。 當您在檢視之間切換時，資訊可能會延遲。 
- 適用於 VM 的 Azure 監視器隨附的 [警示] 摘要，只會顯示從使用受監視 Azure VM 偵測到的健康情況問題所產生的警示。
- 單一 VM 和 Azure 監視器中的 [警示清單] 窗格會顯示在過去 30 天內監視條件設為「已引發」的警示。 警示不是可設定的。 不過，當 [警示清單] 窗格開啟之後，您可以按一下摘要來變更監視條件和時間範圍的篩選條件值。
- 在 [警示清單] 窗格中，我們建議不要修改 [資源類型]、[資源] 和 [監視服務] 篩選條件。 它們是特別為解決方案設定的。 此清單檢視所顯示的欄位，比 [Azure 監視器] > [警示] 清單檢視顯示的還多。   
- 在 Linux VM 中，[健康情況診斷] 檢視會顯示 VM 的完整網域名稱，而不是使用者定義的 VM 名稱。
- 將 VM 關機，即會將部分健全準則更新為「危急」，並將其他健全準則更新為「狀況良好」。 網路 VM 狀態會顯示為「危急」。
- 無法修改健康情況警示嚴重性。 它只能啟用或停用。 此外，某些嚴重性會根據健全準則的狀態來更新。
- 如果您修改健全準則執行個體的任何設定，即會修改 VM 上相同類型的所有健全準則執行個體。 例如，如果對應到邏輯磁碟 C: 的磁碟可用空間健全準則執行個體閾值已修改，那麼，此閾值會套用到已針對相同 VM 探索並監視的所有其他邏輯磁碟。  
- 以 Windows VM 為目標的健全準則閾值均為不可修改的，因為其健全準則會設定為「執行中」或「可用」。 當您從[工作負載監視 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) \(英文\) 查詢健全狀態時，在下列情況中，會針對服務或實體顯示 **LessThan** 或 **GreaterThan** 的 *comparisonOperator* 值且「閾值」為 **4**：
   - DNS 用戶端服務健康情況：服務未執行。 
   - DHCP 用戶端服務健康情況：服務未執行。 
   - RPC 用戶端服務健康情況：服務未執行。 
   - Windows 防火牆服務健康情況：服務未執行。
   - Windows 事件記錄服務健康情況：服務未執行。 
   - 伺服器服務健康情況：服務未執行。 
   - Windows 遠端管理服務健康情況：服務未執行。 
   - 檔案系統錯誤或損毀：邏輯磁碟無法使用。

- 下列 Linux 健全準則的閾值不可修改，因為其健全狀態已設定為 *true*。 從工作負載監視 API 進行查詢時，實體的健全狀態會根據其內容顯示值為 **LessThan** 的 *comparisonOperator* 且「閾值」為 **1**：
   - 邏輯磁碟狀態：邏輯磁碟未連線/無法使用
   - 磁碟狀態：磁碟未連線/無法使用
   - 網路介面卡狀態 - 網路介面卡已停用  

- Windows 中的「CPU 使用率總計」健全準則會在入口網站及工作負載監視 API 中顯示**不等於 4** 的閾值。 當「CPU 使用率總計」大於 95% 且系統佇列長度大於 15 時，即會達到此閾值。 在此版本中無法修改此健全準則。 
- 設定變更 (例如更新閾值) 最多需要 30 分鐘，即使入口網站或工作負載監視 API 可能會立即更新它們也一樣。 
- 個別處理器與邏輯處理器層級健全準則無法在 Windows 中使用。 只有「CPU 使用率總計」適用於 Windows VM。 
- 為每個健全準則所定義的警示規則不會顯示於 Azure 入口網站中。 您只能在[工作負載監視 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) \(英文\) 中啟用或停用健康情況警示規則。 
- 您無法從 Azure 入口網站指派健康情況警示的 [Azure 監視器動作群組](../../azure-monitor/platform/action-groups.md)。 您只能使用通知設定 API，來設定在健康情況警示引發時所要觸發的動作群組。 目前，您可以針對 VM 指派動作群組，因此，對 VM 引發的所有「健康情況警示」會觸發相同的動作群組。 不同於傳統的 Azure 警示，每個健康情況警示規則均不具個別動作群組的概念。 此外，在觸發健康情況警示時，僅支援設定來提供電子郵件或簡訊通知的動作群組。 

## <a name="next-steps"></a>後續步驟
若要了解啟用虛擬機器監視的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-onboard.md)。
