---
title: "Azure 服務匯流排地理災害復原 | Microsoft Docs"
description: "如何使用地理區域，在 Azure 服務匯流排中進行容錯移轉並執行災害復原"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: fdeb9ba55fc8eade95f6fca88f47dd12aa18a480
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2017
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure 服務匯流排地理災害復原

當整個 Azure 區域或資料中心 (如果有任何[可用性區域](../availability-zones/az-overview.md)可用) 經歷的停機時間，很重要的資料處理作業能夠繼續運作的不同區域或資料中心。 因此，「地理災害復原」和「異地複寫」對於任何企業而言都是重要的功能。 Azure 服務匯流排支援地理災害復原和異地複寫，在命名空間層級。 

全域使用服務匯流排 Premium SKU 的地理災害復原功能。 

## <a name="outages-and-disasters"></a>中斷與災害

請務必注意區別 「 中斷 」 和 「 災害 」。 「中斷」是暫時無法使用 Azure 服務匯流排，而且會影響服務的某些元件，例如訊息存放區，或甚至整個資料中心。 不過，在修正問題之後，服務匯流排再次變為無法使用。 中斷通常不會導致訊息或其他資料遺失。 這類中斷的範例可能是資料中心停電。 某些中斷會只因為暫時性或網路問題而簡短的連接中斷。 

A*災害*定義為永久狀態，或長期遺失的服務匯流排的叢集、 Azure 區域或資料中心。 區域或資料中心可能會或可能不會變成可用，或可能已關閉的時數或天數。 這類災害的範例包括火災、水災或地震。 會變成永久損毀可能會導致某些訊息、 事件或其他資料遺失。 不過，在大部分情況下，應該不會遺失資料，而且在備份資料中心之後，就可以復原訊息。

Azure 服務匯流排的地理災害復原功能就是一個災害復原解決方案。 本文中所述的概念和工作流程適用於災害案例，不適用暫時性或暫時中斷。 如需 Microsoft Azure 中災害復原的詳細討論，請參閱[本文](/azure/architecture/resiliency/disaster-recovery-azure-applications)。   

## <a name="basic-concepts-and-terms"></a>基本概念與術語

嚴重損壞修復功能會實作中繼資料損毀修復，並依賴主要和次要災害復原命名空間。 請注意，地理災害復原功能可供[Premium SKU](service-bus-premium-messaging.md)只。 您不需要進行任何連接字串變更，因為連接是透過別名建立的。

本文中使用下列術語：

-  *別名*： 災害復原組態所設定的名稱。 別名提供單一穩定完整網域名稱 (FQDN) 連接字串。 應用程式會使用這個別名連接字串連接到命名空間。 

-  *主要/次要命名空間*： 別名對應的命名空間。 在主要命名空間是 「 作用中 」，及接收訊息 （這可以是現有或新的命名空間）。 次要命名空間是 「 被動 」，而且不會接收訊息。 使兩者都能夠順暢地接受訊息而不變更任何應用程式程式碼或連接字串，是同步，兩者之間的中繼資料。 若要確保只有使用中的命名空間會接收訊息，您必須使用別名。 

-  *中繼資料*： 實體，例如佇列、 主題和訂用帳戶; 和服務的命名空間相關聯的屬性。 請注意只有實體和其設定會自動複寫。 訊息不會複寫。 

-  容錯移轉：啟用次要命名空間的程序。

## <a name="setup-and-failover-flow"></a>安裝和容錯移轉流程

下一節是容錯移轉程序的概觀，並說明如何設定初始的容錯移轉。 

![1][]

### <a name="setup"></a>設定

第一次建立或使用現有的主要命名空間，並為新的次要命名空間，然後配對這兩個。 此配對會為您提供可用來連接的別名。 由於您使用別名，因此不需變更連接字串。 只需將新的命名空間新增至您的容錯移轉配對。 最後，您應該加入某些監視來偵測是否需要在容錯移轉。 在大部分情況下，服務是大型的生態系統的一部分，因此自動容錯移轉，必須執行其餘的子系統或基礎結構與同步經常容錯移轉。

### <a name="example"></a>範例

在此案例的一個範例中，請考慮銷售點 (POS) 解決方案，它會發出訊息或事件。 服務匯流排會將這些事件傳遞至部分對應，或重新格式化方案，然後轉送到另一個系統，以便進一步處理的對應的資料。 此時，所有這些系統可能會裝載於相同的 Azure 區域。 關於何時與容錯移轉的哪個部分取決於基礎結構中的資料流量的決策。 

您可以自動容錯移轉與監視系統，或使用自訂的監視解決方案。 不過，這類自動化會採用額外的計劃和工作，也就是不在本文的範圍。

### <a name="failover-flow"></a>容錯移轉流程

如果您起始容錯移轉，兩個步驟是必要的：

1. 發生另一個中斷時，您想要能夠容錯移轉的一次。 因此，設定另一個被動的命名空間，並更新配對。 

2. 一次使用之後，提取從先前的主要命名空間的訊息。 之後，使用該命名空間規則傳訊之外地理修復安裝或刪除舊的主要命名空間。

> [!NOTE]
> 支援失敗轉寄語意。 在此案例中，您容錯移轉，並重新配對與新的命名空間。 不支援回失敗;例如，在 SQL 叢集。 

![2][]

## <a name="management"></a>管理

如果您犯了;例如，您在初始安裝期間配對錯誤的區域，可以中斷在任何時間的兩個命名空間配對。 如果您想要做為規則的命名空間配對的命名空間，請刪除別名。

## <a name="use-existing-namespace-as-alias"></a>使用現有的命名空間，做為別名

如果您有的案例，您無法在其中變更的產生者和消費者連接時，您可以重複命名空間名稱使用做為別名的名稱。 請參閱[範例程式碼在 GitHub 上的這裡](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)。

## <a name="samples"></a>範例

[GitHub 上的範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)示範如何設定和起始容錯移轉。 這些範例會示範下列概念：

- 使用 Azure 資源管理員具有服務匯流排的 Azure Active Directory 中所需的設定。 
- 若要執行範例程式碼所需的步驟。 
- 傳送和接收來自目前的主要命名空間。 
- 如何使用現有的命名空間，做為別名。

## <a name="considerations"></a>注意事項

請注意，本版記住下列考量：

1. 在容錯移轉規劃，您也應該考慮時間因素。 例如，如果您中斷連線的時間超過 15 到 20 分鐘，您可能會決定起始容錯移轉。 
 
2. 沒有資料會複寫事實表示目前作用中工作階段未複寫。 此外，重複的偵測和排程的訊息可能無法運作。 新的工作階段，排定的訊息及新的重複項目會成功。 

3. 容錯移轉複雜分散式基礎結構應[演練](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation)至少一次。 

4. 同步處理實體可能需要一些時間，大約每分鐘 50-100 個實體。 訂用帳戶和規則也算是實體。 

## <a name="next-steps"></a>後續步驟

- 請參閱地理災害復原在[這裡的 REST API 參考](/rest/api/servicebus/disasterrecoveryconfigs)。
- 執行地理災害復原在 [GitHub 上的範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) \(英文\)。
- 請參閱地理災害復原[範例以將訊息傳送至別名](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) \(英文\)。

若要深入了解服務匯流排傳訊，請參閱下列文章：

* [服務匯流排基本概念](service-bus-fundamentals-hybrid-solutions.md)
* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
