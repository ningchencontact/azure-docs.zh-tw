---
title: 適用於 VM 的 Azure 監視器 (預覽) 已知問題 | Microsoft Docs
description: 本文涵蓋「適用於 VM 的 Azure 監視器」的已知問題，此監視器是 Azure 中的一個解決方案，其中結合了 Azure VM 作業系統的健康情況、應用程式相依性探索及效能監視。
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
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: 038c6afe94ccfea707eea3b4032a2e45f69e5102
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187069"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>適用於 VM 的 Azure 監視器 (預覽) 已知問題

本文涵蓋「適用於 VM 的 Azure 監視器」的已知問題，此監視器是 Azure 中的一個解決方案，其中結合了 Azure VM 作業系統的健康情況、應用程式元件探索及效能監視。 

## <a name="health"></a>健康情況 
以下是目前版本的「健康情況」功能已知問題：

- 如果移除或刪除了 Azure VM，該 VM 將在 VM 清單檢視中顯示一段時間。 此外，按一下已移除或已刪除 VM 的狀態，即會開啟 [健康情況診斷] 檢視，繼而初始載入迴圈。 選取已刪除 VM 的名稱會開啟一個窗格，並顯示一則指出 VM 已刪除的訊息。
- 設定變更 (例如更新閾值) 最多需要 30 分鐘，即使入口網站或工作負載監視 API 可能會立即更新它們也一樣。 
- [健康情況診斷] 體驗的更新速度比其他檢視還快。 當您在它們之間切換時，資訊可能會延遲。 
- 將 VM 關機，即會將部分健全準則更新為「危急」，並將其他健全準則更新為「狀況良好」。 網路 VM 狀態會顯示為「危急」。
- 針對 Linux VM，為單一 VM 檢視列出健康情況準則的頁面標題會包含 VM 的完整網域名稱，而不是使用者定義的 VM 名稱。 
- 在您使用其中一種支援的方法來停用對 VM 的監視之後，而嘗試再次部署它時，應該將它部署在相同的工作區中。 如果您選擇不同的工作區並嘗試檢視該 VM 的健全狀態，它可能會顯示不一致的行為。
- Windows 的 [CPU 使用率總計] 健全準則會顯示一個「不等於」**4** 的閾值，意謂著 CPU 使用率大於 95% 且系統佇列長度大於 15。 此健全準則在此預覽版中不可設定。  
- 從工作區中移除解決方案元件之後，您可能會繼續看到來自 Azure VM 的健全狀態；特別是效能和對應資料，當您在入口網站中瀏覽至上述任一檢視時，就會看到該資料。 資料最終會在一段時間後停止出現在 [效能] 和 [對應] 檢視中；不過，[健康情況] 檢視會繼續顯示 VM 的健康情況狀態。 只有從 [效能] 和 [對應] 檢視，才能讓 [立即試用] 選項重新上線。

## <a name="next-steps"></a>後續步驟
若要了解啟用虛擬機器監視的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器](vminsights-onboard.md)。
