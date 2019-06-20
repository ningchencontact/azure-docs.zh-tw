---
title: Azure Service Fabric 應用程式設計最佳做法 |Microsoft Docs
description: 開發 Service Fabric 應用程式的最佳作法。
services: service-fabric
documentationcenter: .net
author: markfussell
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: msfussell
ms.openlocfilehash: 30d696337061ade6b79c7ec0e4c4de67651f0dad
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203444"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 應用程式設計最佳做法

本文章提供 Azure Service Fabric 上建置應用程式和服務的最佳作法指引。
 
## <a name="get-familiar-with-service-fabric"></a>熟悉 Service Fabric
* 讀取[所以您想要了解 Service Fabric？](service-fabric-content-roadmap.md)文章。
* 了解[Service Fabric 應用程式案例](service-fabric-application-scenarios.md)。
* 閱讀以了解程式設計模型選擇[Service Fabric 程式設計模型概觀](service-fabric-choose-framework.md)。



## <a name="application-design-guidance"></a>應用程式的設計指引
熟悉[一般架構](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)的 Service Fabric 應用程式及其[設計考量](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)。

### <a name="choose-an-api-gateway"></a>選擇 API 閘道
使用 API 閘道服務可以相應放大的後端服務進行通訊。使用的最常見的 API 閘道服務有：

- [Azure API 管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview)，即[與 Service Fabric 整合](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)。
- [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)或是[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)，並使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)從事件中樞分割區讀取。
- [Træfik 反向 proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)，並使用[Azure Service Fabric 提供者](https://docs.traefik.io/configuration/backends/servicefabric/)。
- [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)。

   > [!NOTE] 
   > 使用 Service Fabric，不是直接整合 azure 應用程式閘道。 Azure API 管理通常是偏好的選擇。
- 您自己量身打造[ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web 應用程式閘道。

### <a name="stateless-services"></a>無狀態服務
我們建議您一律先使用來建置無狀態服務[Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)並儲存狀態的 Azure 資料庫、 Azure Cosmos DB 或 Azure 儲存體中。 已外部化的狀態會是適用於大部分的開發人員更熟悉的方法。 這個方法也可讓您利用存放區上的查詢功能。  

### <a name="when-to-use-stateful-services"></a>使用具狀態服務的時機
當您有低延遲的案例且需要保持接近計算的資料，請考慮具狀態服務。 某些案例的範例包括數位對應項的 IoT 裝置、 遊戲狀態、 工作階段狀態快取從資料庫和長時間執行工作流程來追蹤呼叫其他服務的資料。

決定資料保留時間範圍：

- **快取資料**。 使用快取時延遲至外部存放區會造成問題。 使用具狀態服務，為您自己的資料快取，或考慮使用[開放原始碼 SoCreate Service Fabric 分散式快取](https://github.com/SoCreate/service-fabric-distributed-cache)。 在此案例中，您如果不需要擔心會遺失快取中的所有資料。
- **時間繫結資料**。 在此案例中，您需要讓資料更接近計算一段時間的延遲，但您能夠在資料遺失*災害*。 比方說，在許多 IoT 解決方案中，資料必須要關閉計算，例如當會計算平均溫度，過去幾天，但這項資料遺失時，記錄的特定資料點不重要。 此外，在此案例中通常無關緊要，備份的個別資料點。 您只會備份會定期寫入外部儲存體的計算平均值。  
- **長期資料**。 可靠的集合可以永久儲存您的資料。 但在此情況下您需要[準備進行災害復原](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)，包括[設定定期備份原則](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup)叢集。 作用中，您設定如果您的叢集時，嚴重損壞，會發生什麼情況、 您想要建立新的叢集，以及如何部署新的應用程式執行個體，並從最新的備份復原。

節省成本並改善可用性：
- 您可以從遠端存放區中，降低成本，藉由使用具狀態服務，因為您不會產生資料存取和交易成本，因為您不需要使用另一個服務，例如 Azure 快取的 Redis。
- 使用具狀態服務，主要是針對儲存體而非計算耗費資源，而我們不建議。 具狀態服務視為不耗費資源的本機儲存體與計算。
- 藉由移除其他服務的相依性，您可以改善您的服務可用性。 管理與 HA 叢集中的狀態會隔離您與其他服務停機或延遲問題。

## <a name="how-to-work-with-reliable-services"></a>如何使用 Reliable Services
Service Fabric Reliable Services 可讓您輕鬆地建立無狀態與具狀態服務。 如需詳細資訊，請參閱 < [Reliable Services 簡介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)。
- 受限於[取消語彙基元](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps)中`RunAsync()`無狀態與具狀態服務的方法和`ChangeRole()`對於具狀態服務的方法。 如果您不知道，Service Fabric 並不知道如果可以關閉您的服務。 例如，如果您不接受取消語彙基元，就可能會發生更長的應用程式升級的時間。
-   開盤和收盤[通訊接聽程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication)以即時的方式，而且接受取消語彙基元。
-   永遠不會混合使用非同步程式碼的同步處理程式碼。 例如，不要使用`.GetAwaiter().GetResult()`在非同步呼叫。 使用非同步*一路*透過呼叫堆疊。

## <a name="how-to-work-with-reliable-actors"></a>如何使用 Reliable Actors
Service Fabric Reliable Actors 可讓您輕鬆地建立可設定狀態、 虛擬動作項目。 如需詳細資訊，請參閱 < [Reliable Actors 簡介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)。

- 認真考慮使用發佈/訂閱訊息之間調整您的應用程式的動作項目。 提供這項服務的工具包括[開放原始碼 SoCreate Service Fabric Pub/Sub](https://service-fabric-pub-sub.socreate.it/)並[Azure 服務匯流排](https://docs.microsoft.com/azure/service-bus/)。
- 動作項目狀態為[儘可能細分](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。
- 管理[動作項目的生命週期](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。 如果您不打算再次使用，請刪除動作項目。 刪除不必要的動作項目是特別重要，當您使用[變動性狀態提供者](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)，因為所有的狀態會儲存在記憶體中。
- 因為他們[回合式並行](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)，動作項目最適合使用做為獨立的物件。 請勿建立多個動作項目、 同步的方法呼叫 （每個最有可能成為個別的網路呼叫） 的圖形，或建立循環的動作項目要求。 這些將會大幅影響效能和規模。
- 請勿混合使用非同步程式碼的同步處理程式碼。 若要防止發生效能問題，一致地使用非同步。
- 不要在動作項目中進行長時間執行的呼叫。 長時間執行的呼叫將會封鎖其他對相同的動作項目，因為回合式並行處理的呼叫。
- 如果您使用與其他服務通訊[Service Fabric 遠端處理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting)和您要建立`ServiceProxyFactory`，建立該家工廠[動作項目服務](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using)層級和*不*動作項目層級。


## <a name="application-diagnostics"></a>應用程式診斷
關於新增徹底[應用程式記錄](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)服務呼叫中。 它會協助您診斷的案例中的服務彼此呼叫。 比方說，當呼叫 B 呼叫 C 呼叫 D，則呼叫可能任意處失敗。 如果您沒有足夠的記錄，失敗會很難診斷。 如果服務因為呼叫磁碟區太多記錄，請務必至少記錄的錯誤和警告。

## <a name="iot-and-messaging-applications"></a>IoT 和傳訊應用程式
當您正在閱讀來自[Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)或是[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)，使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)。 維護分割從事件中樞讀取的狀態，並將新訊息推送至您的服務，透過 Service Fabric Reliable Services 整合 ServiceFabricProcessor`IEventProcessor::ProcessEventsAsync()`方法。


## <a name="design-guidance-on-azure"></a>在 Azure 上的設計指引
* 請瀏覽[Azure 架構中心](https://docs.microsoft.com/azure/architecture/microservices/)如需設計指引[在 Azure 上建置微服務](https://docs.microsoft.com/azure/architecture/microservices/)。

* 請瀏覽[開始使用 Azure 遊戲](https://docs.microsoft.com/gaming/azure/)如需設計指引[使用 Service Fabric 的遊戲製作服務](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)。
