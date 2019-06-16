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
ms.openlocfilehash: 6563d6e7c454f44e1a70d725191e56d3f90315c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052594"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric 應用程式案例
Azure Service Fabric 提供可靠且彈性的平台您可以在其中撰寫並執行許多類型的商務應用程式和服務。 這些應用程式和微服務可以是無狀態或具狀態，以及它們是為了能更有效率的虛擬機器之間的 資源平衡。 

Service Fabric 的獨特架構可讓您在應用程式中執行接近即時的資料分析、記憶體中計算、平行交易和事件處理。 您可以輕鬆地根據變更的資源需求上下調整 (其實是收發) 您的應用程式。

如需建置應用程式的設計指引，請閱讀[Azure Service Fabric 上的微服務架構](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)並[使用 Service Fabric 的應用程式設計的最佳做法](service-fabric-best-practices-applications.md)。

請考慮使用下列類型的應用程式的 Service Fabric 平台：

* **資料收集、 處理和 IoT**:Service Fabric 會處理較大規模且具備低延遲，透過其具狀態服務。 它可協助數百萬個裝置和計算的資料會共置於其中的裝置上的處理序資料。

    使用 Service Fabric 建置 IoT 服務的客戶包括[Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure)， [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure)， [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure)， [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/)， [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)，並[Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions)。

* **遊戲和工作階段為基礎的互動式應用程式**:Service Fabric 是很有用，如果您的應用程式需要低延遲讀取和寫入，例如線上遊戲或立即訊息中。 Service Fabric 可讓您建置這些互動式、 具狀態的應用程式，而不必建立個別的存放區或快取。 請瀏覽[Azure 遊戲解決方案](https://azure.microsoft.com/solutions/gaming/)如需設計指引[使用 Service Fabric 的遊戲製作服務](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)。

    建置遊戲製作服務的客戶包括[Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure)並[Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/)。 已建立互動式工作階段的客戶包括[Hololens 與 Honeywell](https://customers.microsoft.com/story/honeywell-manufacturing-hololens)。

* **資料分析和工作流程處理**:必須可靠地處理事件或資料流的資料最佳化的讀取和寫入，Service Fabric 中的受益的應用程式。 Service Fabric 也支援應用程式處理管線，其中結果必須可靠地傳遞到下一個處理階段不會遺失任何資料。 這些管線包含交易和財務系統，其中的資料一致性和計算保證是不可或缺。

    已建立的商務工作流程服務的客戶包括[Zeiss 群組](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform)， [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)，並[Société 一般](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric)。

* **在資料上的計算**:Service Fabric 可讓您建置具狀態執行大量資料計算的應用程式。 Service Fabric 應用程式中允許處理 （運算） 和資料共的置。 

   一般來說，當您的應用程式需要存取資料，與外部資料快取或儲存層相關聯的網路延遲會限制運算時間。 Service Fabric 的具狀態服務會消除這種延遲，請啟用更深入的最佳化讀取和寫入。 
   
   例如，請考慮執行近乎即時的建議選項的客戶，低於 100 毫秒的來回時間需求的應用程式。 Service Fabric 服務的延遲和效能特性會提供回應靈敏的經驗給使用者，相較於需要從遠端儲存體擷取必要資料的標準實作模型。 系統是回應速度更快，因為選項建議運算與資料和規則共置。

    已建立計算服務的客戶包括[Solidsoft 回覆](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals)並[Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura)。

* **高可用性服務**：Service Fabric 會提供快速的容錯移轉，藉由建立多個次要服務複本。 如果節點、處理序或個別服務因為硬體或其他故障而停機，其中一個次要複本會升級為主要複本，以便將服務損失降到最低。

* **可調整的服務**：可以分割個別服務，以便在叢集中擴充狀態。 個別服務也會建立並立即移除。 您可以相應放大服務，從幾個節點上的少數執行個體到數千個多節點上的所有執行個體，並視需要調整中。 您可以使用 Service Fabric 來建置這些服務和管理完整生命週期。

## <a name="application-design-case-studies"></a>應用程式設計個案研究
顯示如何使用 Service Fabric 來設計應用程式的個案研究上發佈[客戶經驗談](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=0&so=story_publish_date%20desc/)並[在 Azure 中的微服務](https://azure.microsoft.com/solutions/microservice-applications/)站台。

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>設計應用程式包含無狀態與具狀態微服務
使用 Azure 雲端服務背景工作角色建置應用程式是無狀態服務的範例。 相較之下，具狀態的微服務會維護要求及其回應以外的授權狀態。 這項功能提供高可用性和一致性的狀態，透過簡單的 Api，提供受複寫支援的交易式保證。 

Service Fabric 中的具狀態服務會將所有類型的應用程式，而不只是資料庫和其他資料存放區的高可用性。 這是自然的進展。 針對高可用性，應用程式已經從使用單純的關聯式資料庫進展到 NoSQL 資料庫的境界。 現在應用程式就能在其本身內管理它們的「熱門」狀態和資料，以便進一步提高效能，而不需犧牲可靠性、一致性或可用性。

當您建置由微服務組成的應用程式時，您通常需要的無狀態的 web 應用程式 （例如 ASP.NET 和 Node.js） 組合到無狀態與具狀態的商務中間層服務呼叫。 應用程式和服務所有部署在相同的 Service Fabric 叢集中，透過 Service Fabric 部署命令。 這些服務都各自的規模、 可靠性和資源使用狀況。 這項獨立性提升靈活度和彈性，在開發與生命週期管理。

具狀態的微服務簡化了應用程式設計，因此不需要傳統上為滿足純無狀態應用程式的可用性與延遲需求時所需的其他佇列與快取。 具狀態服務有高可用性和低延遲，因為有較少的詳細資料，以管理您的應用程式中。 

下圖說明設計的應用程式，都是無狀態與具狀態的差異。 藉由運用 [Reliable Services](service-fabric-reliable-services-introduction.md) 和 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 程式設計模型，具狀態服務既可降低應用程式複雜度，又可提高輸送量和降低延遲。

以下是使用無狀態服務的範例應用程式：![使用無狀態服務的應用程式][Image1]

以下是使用具狀態服務的範例應用程式：![使用無狀態服務的應用程式][Image2]

## <a name="next-steps"></a>後續步驟

* 深入了解[模式和案例](service-fabric-patterns-and-scenarios.md)。

* 開始建置無狀態與具狀態服務與 Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md)並[Reliable Actors](service-fabric-reliable-actors-get-started.md)程式設計模型。
* 造訪 Azure Architecture Center 指導方針[在 Azure 上建置微服務](https://docs.microsoft.com/azure/architecture/microservices/)。
* 移至[Azure Service Fabric 應用程式和叢集的最佳作法](service-fabric-best-practices-overview.md)的應用程式的設計指引。

* 另請參閱下列主題：
  * [Microservices 詳細說明](service-fabric-overview-microservices.md)
  * [定義及管理服務狀態](service-fabric-concepts-state.md)
  * [Service Fabric 服務的可用性](service-fabric-availability-services.md)
  * [調整 Service Fabric 服務](service-fabric-concepts-scalability.md)
  * [分割 Service Fabric 服務](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
