---
title: Azure 診斷擴充功能的概觀
description: 使用 Azure 診斷來在雲端服務、虛擬機器及 Service Fabric 中進行偵錯、測量效能、監視、流量分析等。
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 8a287f118c126967d2cf8cad77a434cfecc098eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236238"
---
# <a name="what-is-azure-diagnostics-extension"></a>什麼是 Azure 診斷延伸模組
Azure 診斷延伸模組是 Azure 中的代理程式，可對部署的應用程式收集診斷資料。 您可以使用來自許多不同來源的診斷延伸模組。 目前支援 Azure 雲端服務 (傳統) Web 和背景工作角色、虛擬機器、虛擬機器擴展集和 Service Fabric。 其他 Azure 服務有不同的診斷方法。 請參閱 [Azure 中的監視概觀](../../azure-monitor/overview.md)。

## <a name="linux-agent"></a>Linux 代理程式
[延伸模組的 Linux 版本](../../virtual-machines/extensions/diagnostics-linux.md)適用於執行 Linux 的虛擬機器。 收集的統計資料和行為與 Windows 版本有所不同。

## <a name="data-you-can-collect"></a>您可以收集的資料
Azure 診斷延伸模組可以收集下列類型的資料：

| 資料來源 | 描述 |
| --- | --- |
| 效能計數器計量 |作業系統和自訂效能計數器 |
| 應用程式記錄 |追蹤您應用程式寫入的訊息 |
| Windows 事件日志 |傳送至 Windows 事件記錄系統的資訊 |
| .NET EventSource 記錄 |使用 .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 類別的程式碼編寫事件 |
| IIS 記錄 |IIS 網站的相關資訊 |
| [以資訊清單為基礎的 ETW 記錄](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) \(英文\) |針對任何程序所產生之 Windows 事件所進行的事件追蹤。(1) |
| 損毀傾印 (記錄) |應用程式損毀時之程序狀態的相關資訊 |
| 自訂錯誤記錄 |应用程序或服务创建的日志 |
| Azure 診斷基礎結構記錄 |Azure 診斷本身的相關資訊 |

(1) 若要取得 ETW 提供者的清單，請在您要從中收集資訊的電腦上，在主控台視窗中執行 `c:\Windows\System32\logman.exe query providers`。

## <a name="data-storage"></a>資料儲存體
延伸模組會將其資料儲存在您指定的 [Azure 儲存體帳戶](diagnostics-extension-to-storage.md)中。

您也可以將它傳送到 [Application Insights](../../azure-monitor/app/cloudservices.md)。 

另一個選項是將資料串流到[事件中樞](../../event-hubs/event-hubs-about.md)，以便將資料傳送到非 Azure 監視的服務。

您也可以選擇將資料傳送至 Azure 監視器計量時間序列資料庫。 此時，這個接收只適用於效能計數器。 它可讓您以自訂計量的形式傳送效能計數器。 這項功能處於預覽狀態。 Azure 監視器接收支援：
* 擷取透過 [Azure 監視器計量 API](https://docs.microsoft.com/rest/api/monitor/) 傳送到 Azure 監視器的所有效能計數器。
* 透過 Azure 監視器中的[計量警示](../../azure-monitor/platform/alerts-overview.md)，對傳送到 Azure 監視器的所有效能計數器設定警示
* 將效能計數器中的萬用字元運算子視為計量上的「執行個體」維度。  例如，如果您收集 "LogicalDisk(\*)/DiskWrites/sec" 計數器，就能夠對「執行個體」維度進行篩選和分割，來針對 VM 上每個邏輯磁碟 (例如 C:) 的 Disk Writes/sec 進行繪製或警示

若要深入了解如何設定此接收，請參閱 [Azure 診斷結構描述文件](diagnostics-extension-schema-1dot3.md)。

## <a name="costs"></a>費用
每個上述選項可能會產生費用。 請務必研究，以避免非預期的物料單。  Application Insights]、 [事件中樞和 Azure 儲存體具有個別擷取相關聯的成本和儲存的時間。 特別是，Azure 儲存體將會包含任何資料，不限次數因此您可能想要降低您的成本在特定時間週期之後清除較舊的資料。    

## <a name="versioning-and-configuration-schema"></a>版本控制和設定結構描述
請參閱 [Azure 診斷版本歷程記錄和結構描述](diagnostics-extension-schema.md)。


## <a name="next-steps"></a>後續步驟
選擇您想要在哪個服務上收集診斷資料，並使用下列文章來開始。 如需特定工作的參考，請使用一般的 Azure 診斷連結。

## <a name="cloud-services-using-azure-diagnostics"></a>使用 Azure 診斷的雲端服務
* 如果您使用 Visual Studio，請參閱[使用 Visual Studio 來追蹤雲端服務應用程式](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)來開始。 否則，請參閱
* [如何使用 Azure 診斷來監視雲端服務](../../cloud-services/cloud-services-how-to-monitor.md)
* [在雲端服務應用程式中設定 Azure 診斷](../../cloud-services/cloud-services-dotnet-diagnostics.md)

如需更進階的主題，請參閱

* [搭配適用於雲端服務的 Application Insights 來使用 Azure 診斷](../../azure-monitor/app/cloudservices.md)
* [使用 Azure 診斷追蹤雲端服務應用程式的流程](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [使用 PowerShell 在雲端服務上設定診斷](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>虛擬機器
* 如果您使用 Visual Studio，請參閱[使用 Visual Studio 來追蹤 Microsoft Azure 虛擬機器](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)來開始。 否则，请参阅
* [在 Azure 虛擬機器上設定 Azure 診斷](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

如需更進階的主題，請參閱

* [使用 PowerShell 在 Azure 虛擬機器上設定診斷](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 Azure Resource Manager 範本建立具有監視和診斷的 Windows 虛擬機器](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
請參閱[監視 Service Fabric 應用程式](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)來開始。 當您抵達這篇文章所在網頁時，可以利用左側的導覽樹狀目錄前往許多其他的 Service Fabric 診斷文章。

## <a name="general-articles"></a>一般文章
* 了解如何[在 Azure 診斷中使用效能計數器](../../cloud-services/diagnostics-performance-counters.md)。
* 如果您在開始診斷，或是在 Azure 儲存體資料表中尋找資料時遇到問題，請參閱[針對 Azure 診斷進行疑難排解](diagnostics-extension-troubleshooting.md)

