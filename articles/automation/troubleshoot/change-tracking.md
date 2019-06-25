---
title: 針對 Azure 變更追蹤問題進行疑難排解
description: 本文提供有關針對變更追蹤進行疑難排解的資訊
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a6610b5cb3f01fc70b1737fc4492e09d9a7637b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61085316"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>針對變更追蹤和清查進行疑難排解

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>案例：Windows 機器不會顯示變更追蹤記錄

#### <a name="issue"></a>問題

對於加入變更追蹤的 Windows 機器，您看不到任何清查或變更追蹤結果。

#### <a name="cause"></a>原因

這個錯誤可能是下列原因所造成：

1. **Microsoft Monitoring Agent** 未執行
2. 正在封鎖連回自動化帳戶的通訊。
3. 無法下載變更追蹤的管理組件。
4. 所要上線的 VM 可能來自一部已安裝 Microsoft Monitoring Agent 但未執行過 sysprep 的所複製機器。

#### <a name="resolution"></a>解決方案

1. 確認 **Microsoft Monitoring Agent** (HealthService.exe) 是否正在機器上執行。
1. 請檢查機器上的**事件檢視器**，並尋找其中有 `changetracking` 字組的任何事件。
1. 請瀏覽[網路規劃](../automation-hybrid-runbook-worker.md#network-planning)，了解必須允許哪些位址和連接埠，變更追蹤才能運作。
1. 請確認下列變更追蹤和清查管理組件存在於本機：
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. 如果使用複製的映像，請先對映像執行 sysprep，然後再安裝 Microsoft Monitoring Agent 代理程式。

如果這些解決方案都不能解決您的問題，而且您連絡了支援服務，則可執行下列命令以在代理程式上收集診斷

在代理程式電腦上，瀏覽至 `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` 並執行下列命令：

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 預設會啟用錯誤追蹤，如果您想要啟用如上述範例的詳細錯誤訊息，請使用 `VER` 參數。 對於資訊追蹤，請在叫用 `StartTracing.cmd` 時使用 `INF`。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]  。
