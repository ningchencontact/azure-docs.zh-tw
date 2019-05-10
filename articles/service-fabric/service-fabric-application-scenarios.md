---
title: 應用程式案例和設計 | Microsoft Docs
description: Service Fabric 中雲端應用程式類別概觀。 討論使用具狀態和無狀態服務的應用程式設計。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/24/2019
ms.author: atsenthi
ms.openlocfilehash: f7ad295eb30d257cd195ae02d5a5b1d85de76bda
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228493"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric 應用程式案例
Azure Service Fabric 提供可靠且彈性的平台，可讓您撰寫及執行許多類型的商務應用程式和服務。 這些應用程式和微服務可以是無狀態或具狀態，而且它們會跨虛擬機器進行資源平衡，以達到最佳效率。 Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。 您可以輕鬆地根據變更的資源需求上下調整 (其實是收發) 您的應用程式。

如需建置應用程式的設計指引，請閱讀[Azure Service Fabric 上的微服務架構](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)和[使用 Service Fabric 的應用程式設計的最佳做法](service-fabric-best-practices-applications.md)

Service Fabric 平台適合下列類型的應用程式：

* **資料收集、 處理和 IoT**:因為 Service Fabric 會處理較大規模且具備低延遲，透過其具狀態服務，它很適合用於數百萬個裝置上的資料處理裝置和計算的資料會共置於其中。

    已建立使用 Service Fabric 的 IoT 服務的客戶包括[Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)， [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)， [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)， [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)， [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)，並[Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)。

* **遊戲和工作階段為基礎的互動式應用程式**:Service Fabric 是理想，如果您的應用程式需要低延遲讀取和寫入，例如線上遊戲或立即訊息中。 Service Fabric 可讓您建置這些互動式、 具狀態的應用程式，而不必建立個別的存放區或快取。 請瀏覽[Azure 遊戲解決方案](https://azure.microsoft.com/solutions/gaming/)如需設計指引[使用 Service Fabric 的遊戲製作服務](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)

    建置遊戲製作服務的客戶包括[Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure)並[Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)。 已建立互動式工作階段的客戶包括[Honeywell Hololens 與](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)

* **資料分析和工作流程處理**:必須可靠地處理事件或資料流也受益於最佳化的讀取和寫入，Service Fabric 中的應用程式。 此外，Service Fabric 支援應用程式處理管線，其中結果必須可靠地傳遞到下一個處理階段不會遺失任何資料。 這其中包括交易和財務系統，其中的資料一致性和計算保證是不可或缺。

    已建立的商務工作流程服務的客戶包括[Zeiss 群組](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)和[Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)

* **在資料上的計算**:Service Fabric 可讓您建置資料計算密集具狀態應用程式。 Service Fabric 應用程式中允許的處理 （運算） 和資料位於相同的位置。 一般來說，當您的應用程式需要存取資料，與外部資料快取或儲存層相關聯的網路延遲會限制運算時間。 使用具狀態的 Service Fabric 服務，消除這種延遲，啟用更深入的最佳化讀取和寫入。 例如，請考慮執行近乎即時的建議選項的客戶，低於 100 毫秒的來回時間需求的應用程式。 （其中選項建議運算是與資料和規則共置） 的 Service Fabric 服務的延遲和效能特性會提供回應靈敏的經驗給使用者相較於標準實作模型不必從遠端儲存體擷取必要的資料。

    已建立計算服務的客戶包括[Solidsoft 回覆](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals)和[Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)

* **高可用性服務**：Service Fabric 會提供快速的容錯移轉，藉由建立多個次要服務複本。 如果節點、處理序或個別服務因為硬體或其他故障而停機，其中一個次要複本會升級為主要複本，以便將服務損失降到最低。

* **可調整的服務**：可以分割個別服務，以便在叢集中擴充狀態。 此外，可以建立並立即移除個別服務。 服務可快速且輕鬆地為幾個節點上的少數執行個體從向外延展至數千個多節點上的所有執行個體，然後再次相應縮小，視您的資源需求而定。 您可以使用 Service Fabric 來建置這些服務和管理完整生命週期。

## <a name="application-design-case-studies"></a>應用程式設計個案研究
已發佈了許多示範如何使用 Service Fabric 來設計應用程式的個案研究[客戶經驗談](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/)並[微服務解決方案網站](https://azure.microsoft.com/solutions/microservice-applications/)。

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>設計由無狀態與具狀態的微服務組成的應用程式
使用「Azure 雲端服務」背景工作角色來建置應用程式，即是一個無狀態服務的範例。 相較之下，具狀態的微服務會維護要求及其回應以外的授權狀態。 這項功能提供高可用性和一致性的狀態，透過簡單的 Api，提供受複寫支援的交易式保證。 Service Fabric 的具狀態服務會將高可用性普及至所有類型的應用程式，而不會侷限於資料庫和其他資料存放區。 這是自然的進展。 針對高可用性，應用程式已經從使用單純的關聯式資料庫進展到 NoSQL 資料庫的境界。 现在，应用程序可在自身内部管理其“热”状态和数据，以便进一步提高性能，而无需损失可靠性、一致性或可用性。

建置由微服務組成的應用程式時，您通常會擁有無狀態 Web Apps (ASP.NET、Node.js 等等) 的組合，呼叫無狀態和具狀態的商務中間層服務，使用 Service Fabric 部署命令，全部部署到相同的 Service Fabric 叢集。 這些服務都各自的規模、 可靠性和資源使用狀況，大幅提升靈活度和彈性，在開發與生命週期管理。

具狀態的微服務簡化了應用程式設計，因此不需要傳統上為滿足純無狀態應用程式的可用性與延遲需求時所需的其他佇列與快取。 具狀態服務自然會有高可用性和低延遲，因為會有較少的移動組件，來管理整體應用程式中。 下列圖表說明設計無狀態與具狀態之應用程式間的差異。 藉由運用 [Reliable Services](service-fabric-reliable-services-introduction.md) 和 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 程式設計模型，具狀態服務既可降低應用程式複雜度，又可提高輸送量和降低延遲。

## <a name="an-application-built-using-stateless-services"></a>使用無狀態服務建置的應用程式
![使用無狀態服務的應用程式][Image1]

## <a name="an-application-built-using-stateful-services"></a>使用可設定狀態的服務建置的應用程式
![使用无状态服务的应用程序][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

* 閱讀[客戶案例研究](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc)
* 深入了解[模式和案例](service-fabric-patterns-and-scenarios.md)

* 開始使用 Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) 和 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 程式設計模型，來建置無狀態與具狀態的服務。
* 造訪指導方針的 Azure 架構中心[在 Azure 上建置微服務](https://docs.microsoft.com/azure/architecture/microservices/)
* 移至[Azure Service Fabric 應用程式和叢集的最佳作法](service-fabric-best-practices-overview.md)的應用程式的設計指引。

* 另請參閱下列主題：
  * [Microservices 詳細說明](service-fabric-overview-microservices.md)
  * [定義及管理服務狀態](service-fabric-concepts-state.md)
  * [Service Fabric 服务的可用性](service-fabric-availability-services.md)
  * [調整 Service Fabric 服務](service-fabric-concepts-scalability.md)
  * [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
