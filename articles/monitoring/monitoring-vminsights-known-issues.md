---
title: 適用於 VM 的 Azure 監視器已知問題 | Microsoft Docs
description: 適用於 VM 的 Azure 監視器是結合了 Azure VM 作業系統健康情況與效能監控的 Azure 解決方案，其會自動探索應用程式元件和與其他資源的相依性，並且會對應它們之間的通訊。 本文涵蓋各項已知問題。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2018
ms.author: magoedte
ms.openlocfilehash: 6d1f1d1ae07ec32262f655fd6ed7205a70e252f4
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385078"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器已知問題

下列為「適用於 VM 的 Azure 監視器」健康情況功能的已知問題：

- 健康情況功能會上架到所有連線到 Log Analytics 工作區的 VM，即使是從單一 VM 起始並完成上架亦然。
- 如果 Azure VM 因已遭移除或刪除而不再存在，它將會顯示在 VM 清單檢視中三至七天。 此外，按一下已移除或已刪除 VM 的狀態將會啟動 [健康情況診斷] 檢視，繼而進入載入迴圈。 選取已刪除 VM 的名稱將會啟動刀鋒視窗，並顯示一則指出 VM 已刪除的訊息。
- 此版本的健康情況準則時段和頻率無法修改。 
- 無法停用健康情況準則。 
- 上線之後，可能需要一點時間，資料才會顯示在 [Azure 監視器] -> [虛擬機器]-> [健康情況] 或是 [VM 資源] 刀鋒視窗-> [深入解析] 中
- 健康情況診斷體驗比任何其他檢視的更新速度更快，因此您在檢視之間切換時，資訊可能會延遲出現  
- 「適用於 VM 的 Azure 監視器」健康情況中提供的警示摘要，僅適用於受監視 Azure VM 偵測到的健康情況問題所引發的警示。
- 單一 VM 和 Azure 監視器中的 [警示清單] 檢視頁面會顯示在過去 30 天內監視條件設為「引發」的警示。  您無法對其進行設定。 不過，按一下摘要之後，一旦啟動 [警示清單] 檢視頁面，您就可以變更監視條件和時間範圍的篩選條件值。
- 在 [警示清單] 檢視頁面上，建議您不要修改 [資源類型]、[資源] 及 [監視服務] 篩選條件，因為這些篩選條件已設定為專用於解決方案 (相較於 [Azure 監視器] -> [警示] 清單檢視，此清單檢視會額外顯示某些欄位)。    
- 在 Linux VM 中，[健康情況診斷] 檢視具有 VM 的完整網域名稱，而不是使用者定義的 VM 名稱。
- 關閉 VM 會將其一些健康情況準則更新為重大狀態，並將其他健康情況準則更新為狀況良好狀態，且 VM 的淨狀態處於重大狀態。
- 無法修改健康情況警示的嚴重性，只能加以啟用或停用。  此外，某些嚴重性會根據健康情況準則的狀態來更新。
- 修改健康情況準則執行個體的任何設定，將會導致修改 VM 上相同類型的所有健康情況準則執行個體中的相同設定。 例如，如果對應至邏輯磁碟 C: 的磁碟可用空間健康情況準則執行個體閾值經過修改，那麼此閾值會套用到針對相同 VM 探索並監視的所有其他邏輯磁碟。   
- 下列以 Windows VM 為目標的健康情況準則不可修改閾值，因為其健康狀態已設定為 [執行中] 或 [可用]。 從[工作負載監視 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) 進行查詢時，在下列情況下，服務或實體的健康狀態會顯示 **LessThan** 或 **GreaterThan** 的 *comparisonOperator* 值，並顯示閾值 **4**：
   - DNS 用戶端服務健康情況 – 服務未執行 
   - DHCP 用戶端服務健康情況 – 服務未執行 
   - RPC 服務健康情況 – 服務未執行 
   - Windows 防火牆服務健康情況 – 服務未執行
   - Windows 事件記錄服務健康情況 – 服務未執行 
   - 伺服器服務健康情況 – 服務未執行 
   - Windows 遠端管理服務健康情況 – 服務未執行 
   - 檔案系統錯誤或損毀 – 邏輯磁碟無法使用

- 下列 Linux 健康情況準則的閾值不可修改，因為其健康狀態已設定為 **true**。  從工作負載監視 API 進行查詢時，實體的健康狀態會根據其內容顯示值為 **LessThan** 的 *comparisonOperator* 和閾值 **1**：
   - 邏輯磁碟狀態 – 邏輯磁碟未連線/無法使用
   - 磁碟狀態 – 磁碟未連線/無法使用
   - 網路介面卡狀態 - 網路介面卡已停用  

- Windows 中的 **CPU 總使用率**健康情況準則會在入口網站中顯示**不等於 4** 的臨界值，且從工作負載監視 API 進行查詢，如果 CPU 使用率大於 95%，系統佇列長度將大於 15。 在此版本中無法修改此健康情況準則。  
- 設定變更 (例如更新閾值) 最多需要 30 分鐘才會生效，即使入口網站或工作負載監視 API 可能會立即更新，仍是如此。  
- 在 Windows 中無法使用個別處理器與邏輯處理器層級的健康情況準則，只有 **CPU 總使用率**適用於 Windows VM。  
- 為每個健康情況準則定義的警示規則不會公開在 Azure 入口網站中。 您只能從[工作負載監視 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) 加以設定，以啟用或停用健康情況警示規則。  
- 您無法從 Azure 入口網站指派健康情況警示的 [Azure 監視器動作群組](../monitoring-and-diagnostics/monitoring-action-groups.md)。 您必須使用通知設定 API，設定在健康情況警示引發時所要觸發的動作群組。 目前，您可以針對 VM 指派動作群組，而對觸發相同動作群組的 VM 引發所有的*健康情況警示*。 如同傳統的 Azure 警示，每個健康情況警示規則並不會有個別的動作群組。 此外，在健康情況警示觸發時，僅支援設定為藉由傳送電子郵件或簡訊進行通知的動作群組。 

## <a name="next-steps"></a>後續步驟
檢閱[將適用於 VM 的 Azure 監視器上線](monitoring-vminsights-onboard.md)，了解啟用虛擬機器監視的需求和方法。