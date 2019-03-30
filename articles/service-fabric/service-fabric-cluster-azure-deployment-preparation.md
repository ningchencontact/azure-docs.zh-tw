---
title: 規劃 Azure Service Fabric 叢集部署 |Microsoft Docs
description: 深入了解規劃及準備實際執行 Service Fabric 叢集部署至 Azure。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663233"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>規劃及準備叢集部署

規劃及準備生產環境叢集部署是非常重要。  有許多因素需要考量。  這篇文章會引導您完成準備叢集部署的步驟。

## <a name="read-the-best-practices-information"></a>讀取的最佳作法資訊
若要成功地管理 Azure Service Fabric 應用程式和叢集，有我們強烈建議您執行以最佳化您的生產環境的可靠性的作業。  如需詳細資訊，請參閱[Service Fabric 應用程式和叢集的最佳作法](service-fabric-best-practices-overview.md)。

## <a name="select-the-os-for-the-cluster"></a>選取叢集作業系統
Service Fabric 可讓您在執行 Windows Server 或 Linux 的任何 VM 或電腦上建立 Service Fabric 叢集。  在部署您的叢集，您必須選擇作業系統：Windows 或 Linux。  在叢集中的每個節點 （虛擬機器） 執行相同的作業系統時，您不能在相同的叢集中混合 Windows 和 Linux Vm。

## <a name="capacity-planning"></a>容量規劃
對於任何生產部署而言，容量規劃都是一個很重要的步驟。 以下是一些您在該程序中必須考量的事情。

* 適用於您叢集的節點類型的初始數目 
* 屬性的每個節點類型 （大小、 執行個體數目、 主要、 網際網路對向、 Vm 等等的數字）。
* 叢集的可靠性和持久性的特性

### <a name="select-the-initial-number-of-node-types"></a>選取節點類型的初始的數目
首先，您必須找出您要建立之叢集的用途。 您計劃將哪些類型的應用程式部署到此叢集中？ 您的應用程式是否有多個服務，而且其中是否有任何服務必須是公開或網際網路對向的服務？ 您 (構成應用程式) 的服務是否有不同的基礎結構需求，例如，更多的 RAM 或更高的 CPU 週期？ Service Fabric 叢集可以包含多個節點類型： 主要節點類型和一或多個非主要節點類型。 每個節點類型都會對應到虛擬機器擴展集。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。 [節點屬性和放置條件約束][ placementconstraints]可以限制特定節點類型的特定服務設定。  如需詳細資訊，請參閱[需要一開始您的叢集的節點類型數目](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)。

### <a name="select-node-properties-for-each-node-type"></a>選取每個節點類型的節點屬性
節點類型定義相關聯的擴展集中的 VM SKU、 號碼和 Vm 的屬性。

取決於每個節點類型的最小的 Vm 大小[持久性層][ durability]您選擇的節點型別。

取決於主要節點類型的最小的 Vm 數目[可靠性層級][ reliability]您選擇。

請參閱的最低建議事項[主要節點類型](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)，[非主要節點類型上的可設定狀態工作負載](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)，並[非主要節點類型上的無狀態工作負載](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)。 

任何超過節點數目下限應該根據您想要在此節點類型中執行的應用程式/服務複本數目。  [Service Fabric 應用程式的容量規劃](service-fabric-capacity-planning.md)可協助您預估執行您的應用程式所需的資源。 您一律可以相應增加叢集，或稍後下移來變更應用程式工作負載調整。 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>選取叢集的持久性和可靠性層級
持久性層級用來向系統指示您的 VM 對於基本 Azure 基礎結構所擁有的權限。 在主要節點類型中，此權限可讓 Service Fabric 暫停會影響系統服務及具狀態服務的仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像，或 VM 移轉)。 在非主要節點類型中，此權限可讓 Service Fabric 暫停會影響具狀態服務之仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像和 VM 移轉)。  如要使用，請參閱 < 層級的建議與不同層級的優點[叢集的持久性特性][durability]。

可靠性層級用來設定您想要在此叢集中的主要節點類型上執行的系統服務複本數目。 複本數目越多，叢集中的系統服務越可靠。  如要使用，請參閱 < 層級的建議與不同層級的優點[叢集的可靠性特性][reliability]。 

## <a name="enable-reverse-proxy-andor-dns"></a>啟用反向 proxy 及 （或） DNS
叢集內彼此連接的服務通常可以直接存取其他服務的端點，因為叢集中的節點位於相同的本機網路上。 若要讓服務之間連接的工作變得更容易，Service Fabric 會提供額外的服務：A [DNS 服務](service-fabric-dnsservice.md)並[反向 proxy 服務](service-fabric-reverseproxy.md)。  部署叢集時，可以啟用這兩項服務。

由於許多服務，特別是容器化的服務，都可以有現有的 URL 名稱，就能夠解決這些問題使用標準 DNS 通訊協定 （而不是 「 命名服務通訊協定） 是很方便，尤其是在應用程式 「 隨即轉移 」 案例。 這就是 DNS 服務的功能所在。 它可讓您將 DNS 服務對應到某個服務名稱，再由此解析端點 IP 位址。

反向 proxy 可處理叢集中公開 HTTP 端點 （包括 HTTPS） 的服務。 反向 proxy 可大幅簡化呼叫其他服務提供特定的 URI 格式。  反向 proxy 也會處理解析、 連接和重試一次一個服務彼此通訊所需的步驟。

## <a name="prepare-for-disaster-recovery"></a>準備進行災害復原
提供高可用性的關鍵在於確保服務能夠承受所有不同類型的故障。 這對於預料之外且無法控制的故障而言特別重要。 [準備進行災害復原](service-fabric-disaster-recovery.md)說明一些常見的故障模式，可能會造成嚴重損壞，如果不建立模型和正確管理。 此外，它也會討論緩和措施及發生災害時要採取的動作。

## <a name="production-readiness-checklist"></a>實際執行整備檢查清單
您的應用程式和叢集已經準備好要接受生產環境流量嗎？ 再到生產環境中部署您的叢集，執行[生產環境的整備檢查清單](service-fabric-production-readiness-checklist.md)。 更新您的應用程式和叢集中執行順暢，透過這份檢查清單中的項目。 我們強烈建議進入生產環境之前，檢查所有這些項目。

## <a name="next-steps"></a>後續步驟
* [建立執行 Windows 的 Service Fabric 叢集](service-fabric-best-practices-overview.md)
* [建立執行 Linux 的 Service Fabric 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster