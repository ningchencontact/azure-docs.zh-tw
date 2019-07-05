---
title: Azure 監視器容器區域對應
description: 本文說明 Azure 監視器容器、 Log Analytics 工作區，與自訂計量之間支援的區域對應。
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 481a2a400be4e983e0a2337a200324061494efa1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518075"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>由 Azure 監視器容器支援的區域對應

 只有特定區域時啟用適用於容器的 Azure 監視器，支援連結的 Log Analytics 工作區與 AKS 叢集，並收集自訂計量提交至 Azure 監視器。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 工作區支援的對應

AKS 叢集資源或 Log Analytics 工作區可位於其他區域，以及下表顯示我們的對應。

|**AKS 叢集區域** | **Log Analytics 工作區區域** |
|-----------------------|------------------------------------|
|**非洲** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**澳大利亞** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**亞太地區** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**巴西** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**歐洲** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**印度** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**南韓** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**美國** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|

<sup>1</sup>容量限制，因為區域無法使用時建立新的資源。 這包括 Log Analytics 工作區。 不過，在區域中預先存在連結的資源應該繼續運作。

## <a name="custom-metrics-supported-regions"></a>自訂計量支援的區域

收集計量的 Azure Kubernetes Service (AKS) 叢集節點，並發佈為自訂的度量，只能在下列支援 pod [Azure 區域](../platform/metrics-custom-overview.md#supported-regions)。

## <a name="next-steps"></a>後續步驟

若要開始監視您的 AKS 叢集，請檢閱[如何啟用適用於容器的 Azure 監視器](container-insights-onboard.md)了解需求和可用的方法，若要啟用監視。  