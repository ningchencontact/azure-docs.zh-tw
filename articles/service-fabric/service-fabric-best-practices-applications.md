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
ms.date: 04/26/2019
ms.author: msfussell
ms.openlocfilehash: 55f043effc7cdb102acea856e89c58f660d0cde5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65237743"
---
# <a name="azure-service-fabric-application-design-best-practices"></a>Azure Service Fabric 應用程式設計最佳做法

本文提供 Service Fabric 上建置應用程式和服務的最佳作法指引。
 
## <a name="get-familiar-with-service-fabric"></a>熟悉 Service Fabric
* [因此，您想要了解 Service Fabric？](service-fabric-content-roadmap.md)
* 了解[Service Fabric 應用程式案例](service-fabric-application-scenarios.md)
* 然後了解程式設計模型選擇使用[Service Fabric 程式設計模型概觀](service-fabric-choose-framework.md)



## <a name="application-design-guidance"></a>應用程式的設計指引
熟悉[一般架構](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric)Service Fabric 應用程式並將其[設計考量](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric#design-considerations)。

### <a name="choose-an-api-gateway"></a>選擇 API 閘道
使用 API 閘道服務可以相應放大的後端服務進行通訊。使用的最常見的 API 閘道服務有：

- [Azure API 管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-api-management-overview)，這是[與 Service Fabric 整合](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-api-management)
- [Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)或是[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor)來讀取事件中樞分割區
- [Træfik 反向 proxy](https://blogs.msdn.microsoft.com/azureservicefabric/2018/04/05/intelligent-routing-on-service-fabric-with-traefik/)使用[Azure Service Fabric 提供者](https://docs.traefik.io/configuration/backends/servicefabric/)
- [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)附註： 這樣並未直接整合到 Service Fabric 和 Azure API 管理通常是偏好的選擇
- 建置您自己[ASP.NET Core](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-aspnetcore) web 應用程式閘道

### <a name="choose-stateless-services"></a>選擇無狀態服務
我們建議您一律先建置無狀態服務使用[Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) Azure Database、 Azure CosmosDB 或 Azure 儲存體中儲存狀態。 需要外部化的狀態是適用於大部分的開發人員更熟悉的方法，並可讓您也利用查詢功能上的存放區。  

### <a name="when-to-choose-stateful-services"></a>選擇 具狀態服務的時機
當您有低延遲的案例且需要保持接近計算的資料，請考慮具狀態服務。 範例包括數位對應項的 IoT 裝置、 遊戲狀態、 工作階段狀態快取資料庫中的資料和長時間執行的工作流程來追蹤呼叫其他服務。

決定資料保留時間範圍：

- 快取的資料-您使用快取到外部存放區的延遲會造成問題的地方。 使用具狀態服務，因為您自己的資料快取，或考慮使用[開放原始碼 SoCreate service fabric-分散式-快取](https://github.com/SoCreate/service-fabric-distributed-cache)。 在此案例中，您可能會遺失快取中的所有資料，並不重要。
- 時間繫結的資料-您需要保留資料更接近來計算延遲一段時間，但該資料可以負擔會在遺失*災害*案例。 比方說，許多資料都必須關閉的 IoT 解決方案的計算，例如計算過去幾天內，平均溫度，不過，如果遺失此資料，然後將特定的資料點記錄不重要。 也在此案例中您通常不在乎的個別資料點，只會定期寫入外部儲存體計算平均值的備份。  
- 長期的資料-可靠的集合可以儲存您的資料永久。 不過，在此情況下您需要[準備進行災害復原](https://docs.microsoft.com/azure/service-fabric/service-fabric-disaster-recovery)包括[設定定期備份原則](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-configure-periodic-backup)叢集。 作用中，您設定如果損毀您的叢集在災害情況下，會發生什麼情況、 您想要建立新的叢集，以及如何部署新的應用程式執行個體，並從最新的備份復原。

節省成本並改善可用性：
- 因為您不會造成從遠端存放區的資料存取和交易成本，而不需要使用另一個服務，例如 Azure Redis，您可以減少使用具狀態服務的成本。
- 使用具狀態服務，主要是針對儲存體而非計算是昂貴且不建議使用。 具狀態服務視為不耗費資源的本機儲存體與計算。
- 藉由移除其他服務的相依性，您可以改善您的服務可用性。 具有以 HA 叢集中的狀態會隔離您與其他服務停機或延遲問題。

## <a name="how-to-properly-work-with-reliable-services"></a>如何使用 Reliable Services 正常運作
Reliable Services 可讓您輕鬆地建立無狀態與具狀態服務。 讀取[Reliable Services 簡介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)
- 受限於[取消語彙基元](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-lifecycle#stateful-service-primary-swaps)中`RunAsync()`無狀態與具狀態服務的方法和`ChangeRole()`對於具狀態服務的方法。 如果沒有這麼做，Service Fabric 不知道如果可以關閉您的服務。 比方說，不接受取消語彙基元會導致更長的應用程式升級的時間。
-   開盤和收盤[通訊接聽程式](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication)及時，而且接受取消語彙基元。
-   永遠不會混合使用非同步程式碼的同步處理程式碼。 例如，請勿使用`.GetAwaiter().GetResult()`在您的非同步呼叫; 它必須是非同步*一路*透過呼叫堆疊。

## <a name="how-to-properly-work-with-reliable-actors"></a>如何使用 Reliable Actors 正常運作
Reliable Actors 可讓您輕鬆地建立可設定狀態、 虛擬動作項目。 讀取[Reliable Actors 簡介](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction)

- 請慎重考慮使用發佈/訂閱訊息之間調整應用程式的動作項目。 例如，[開放原始碼 SoCreate pub/sub](https://service-fabric-pub-sub.socreate.it/)或是[Azure 服務匯流排](https://docs.microsoft.com/azure/service-bus/)。
- 動作項目狀態為[儘可能細分](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。
- 管理[動作項目的生命週期](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#best-practices)。 如果您將無法再使用它們，請刪除動作項目。 使用時，這是特別[VolatileState 提供者](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-state-management#state-persistence-and-replication)所有狀態儲存在記憶體中。
- 由於他們[開啟基礎並行](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-introduction#concurrency)動作項目最適合使用做為獨立的物件。 請勿建立多個動作項目、 同步的方法呼叫 （每個最有可能成為個別的網路呼叫） 的圖形或有循環的動作項目要求;這些將會大幅影響效能和規模。
- 請勿混合使用非同步程式碼; 的同步處理程式碼它必須一直是非同步，以防止發生效能問題。
- 沒有動作項目中進行長時間執行的呼叫，它會封鎖其他對相同的動作項目，因為回合式並行處理的呼叫。
- 如果與使用其他服務通訊[Service Fabric 遠端處理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-communication-remoting)，而您需要建立`ServiceProxyFactory`，然後建立該家工廠[動作項目服務](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-using)層級和*不*動作項目層級。


## <a name="application-diagnostics"></a>應用程式診斷
- 新增徹底[應用程式記錄](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)服務呼叫中。 這有助於診斷的情況下，服務彼此呼叫。 例如，當 A]-> [B]-> [C]-> [的 D 的任何位置; 呼叫可能會失敗如果沒有足夠的記錄，，很難診斷。 如果服務因為呼叫磁碟區太多記錄，至少務必來記錄錯誤和警告。

## <a name="iot-and-messaging-applications"></a>IoT 和傳訊應用程式
當讀取訊息[Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/)或是[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/)使用[ServiceFabricProcessor](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/ServiceFabricProcessor) ，整合了 Service Fabric Reliable Services 來維護從事件中樞讀取的狀態會分割並將新訊息推送至您的服務，透過`IEventProcessor::ProcessEventsAsync()`方法。


## <a name="design-guidance-on-azure"></a>在 Azure 上的設計指引
* 請瀏覽[Azure 架構中心](https://docs.microsoft.com/azure/architecture/microservices/)如需設計指引[在 Azure 上建置微服務](https://docs.microsoft.com/azure/architecture/microservices/)

* 請瀏覽[開始使用 Azure 遊戲](https://docs.microsoft.com/gaming/azure/)如需設計指引[使用 Service Fabric 的遊戲製作服務](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf)
