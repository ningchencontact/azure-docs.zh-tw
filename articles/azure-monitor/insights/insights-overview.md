---
title: Azure 監視器中的 Insights 概觀 |Microsoft Docs
description: 深入解析提供自訂監視操作體驗在 Azure 監視器中特定的應用程式和服務。 本文提供每個目前可用的深入解析的簡短描述。
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247225"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure 監視器中的深入解析的概觀
深入解析提供特定的應用程式和服務的自訂監視操作體驗。 它們將資料儲存在[Azure 監視器的資料平台](../platform/data-platform.md)運用其他 Azure 監視器功能，以便分析和警示，但可能會收集其他資料並提供唯一的使用者體驗在 Azure 入口網站中。 存取見解**Insights** Azure 監視器功能表，在 Azure 入口網站中的區段。

下列各節提供 Azure 監視器中目前可用的深入解析的簡短描述。 在每個，請參閱詳細的文件，如需詳細資訊。

## <a name="application-insights"></a>Application Insights
Application Insights 是多個平台上的 Web 開發人員所適用的可延伸「應用程式效能管理」(APM) 服務。 您可以使用它來監視即時 Web 應用程式。 它適用於應用程式在各種平台包括.NET、 Node.js 和 Java EE 裝載在內部部署、 混合或任何公用雲端。 它也會與您的 DevOps 程序整合，並有各種不同的開發工具的連接點。

請參閱[什麼是 Application Insights？](../app/app-insights-overview.md)。

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>適用於容器的 azure 監視器
適用於容器的 Azure 監視器會監視部署至 Azure Container Instances 或 Azure Kubernetes Service (AKS) 上所裝載受控 Kubernetes 叢集的容器工作負載效能。 監視容器很重要，尤其在您使用多個應用程式大規模執行生產環境叢集時。

請參閱[容器概觀相關的 Azure 監視](../insights/container-insights-overview.md)。

![適用於容器的 azure 監視器](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure 資源群組 （預覽） 監視
Azure 資源群組監視有助於分級和診斷時提供內容以至於的健全狀況和效能的資源群組的整體個別資源遇到任何問題。

請參閱[使用 Azure 監視器 （預覽） 監視的資源群組](../insights/resource-group-insights.md)。

![Azure 資源群組監視](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure 監視器的 Vm （預覽）
適用於 VM 的 Azure 監視器會大規模監視您的 Azure 虛擬機器 (VM) 和虛擬機器擴展集。 此服務會分析您 Windows 和 Linux VM 的效能與健康情況，並且在其他資源和外部處理序上監視其處理序及相依性。

請參閱[什麼是適用於 Vm 的 Azure 監視器？](vminsights-overview.md)

![適用於 VM 的 Azure 監視器](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>後續步驟
* 深入了解[Azure 監視器的資料平台](../platform/data-platform.md)利用深入解析。
* 深入了解不同[Azure 監視器所使用的資料來源](../platform/data-sources.md)和不同種類的每個 insights 所收集的資料。
