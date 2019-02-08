---
title: Azure Service Fabric 監視最佳做法 | Microsoft Docs
description: 監視 Service Fabric 叢集和應用程式的最佳做法。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 5e3cfad70dd0cc0c53f6971c9ddce44f0ca25ecd
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104298"
---
# <a name="monitoring-and-diagnostics"></a>監視和診斷

不論任何雲端環境，針對工作負載的開發、測試及部署進行[監視和診斷](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview)都極為重要。 例如，您可以追蹤應用程式被使用的方式、由 Service Fabric 平台所採取的動作、搭配效能計數器的資源使用量，以及叢集的整體健康情況。 您可以使用這些資訊來診斷並修正問題，並防止它們於未來再度發生。

## <a name="application-monitoring"></a>應用程式監視

應用程式監視會追蹤您應用程式的功能與元件使用情況。 監視應用程式，以確定找出影響使用者的問題。 應用程式監視的責任會落在開發應用程式及其服務的使用者身上，因為它會專屬於您應用程式的商務邏輯。 建議您使用 Azure 的應用程式監視工具 [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet) 設定應用程式監視。

## <a name="cluster-monitoring"></a>叢集監視

Service Fabric 的其中一個目標是讓應用程式在硬體失敗時能夠復原。 這個目標可以透過 平台的系統服務偵測基礎結構問題，並快速地將工作負載容錯移轉到叢集中的其他節點來達成。 但如果是系統服務本身有問題的話，會發生什麼情況？ 或如果在嘗試部署或移動工作負載時違反了設置服務的規則，會發生麼情況？ Service Fabric 會提供這些問題和其他問題的診斷，以確保能夠讓您了解 Service Fabric 平台如何與您的應用程式、服務、容器和節點互動。

對於 Windows 叢集，建議您使用[診斷代理程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad)和 [Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup) 設定叢集監視。

對於 Linux 叢集，Log Analytics 也是建議用於 Azure 平台和基礎結構監視的工具。 Linux 平台診斷需要進行 [Syslog 中的 Service Fabric Linux 叢集事件](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog)中所述的不同設定。

## <a name="infrastructure-monitoring"></a>基礎結構監視

建議您使用 [Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) 來監視叢集層級事件。 在依據先前的連結所述，搭配您的工作區設定 Log Analytics 代理程式之後，您將能夠收集效能計量 (例如 CPU 使用率)、.NET 效能計數器 (例如處理程序層級 CPU 使用率)，Service Fabric 效能計數器 (例如可靠服務的例外狀況數目)，以及容器計量 (例如 CPU 使用率)。  您將需要把容器記錄寫入至 stdout 或 stderr，讓它們能夠在 Log Analytics 中提供使用。

## <a name="watchdogs"></a>監視程式

一般而言，監視程式是一個單獨的服務，可以觀察服務之間的健康情況和負載、偵測端點，並回報叢集中非預期的健康情況事件。 這可以避免在單一服務效能中無法偵測到的錯誤。 監視程式也非常適合裝載能夠執行修復動作的程式碼，而無需使用者互動 (例如在特定時間間隔，清除儲存體中的記錄檔)。 請參閱 [Syslot 中的 Service Fabric Linux 叢集事件](https://github.com/Azure-Samples/service-fabric-watchdog-service) 中的範例監視程式服務實作。

## <a name="next-steps"></a>後續步驟

* 開始檢測應用程式：[應用程式層級事件和記錄產生](service-fabric-diagnostics-event-generation-app.md)。
* 透過[監視和診斷 Service Fabric 上的 ASP.NET Core 應用程式](service-fabric-tutorial-monitoring-aspnet.md)中的步驟，來針對您的應用程式設定 Application Insights。
* 深入了解監視平台和 Service Fabric 提供給您的事件：[平台層級事件和記錄產生](service-fabric-diagnostics-event-generation-infra.md)。
* 設定 Log Analytics 與 Service Fabric 的整合：[為叢集設定 Log Analytics](service-fabric-diagnostics-oms-setup.md)
* 了解如何設定 Log Analytics 以監視容器：[監視和診斷 Azure Service Fabric 中的 Windows 容器](service-fabric-tutorial-monitoring-wincontainers.md)。
* 請參閱[診斷常見案例](service-fabric-diagnostics-common-scenarios.md)中的 Service Fabric 診斷問題和解決方案範例
* 了解 Azure 資源的一般監視建議：[最佳做法 - 監視和診斷](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。